---
title: Workers实现Api白名单
date: 2025-12-22 09:46:02
tags:
  - 技术
  - 前端
  - 后端
  - 总结
cover: 0.jpg
background: url(0.jpg)
---
# 前言

&nbsp; &nbsp; &nbsp; 在八月份的一篇文章中，我介绍了一种调用deepseek进行文章总结的方法，{% btn 'https://2am.top/2025/08/08/%E5%8D%9A%E5%AE%A2%E6%8E%A5%E5%85%A5deepseek%E6%91%98%E8%A6%81/',去看看,far fa-hand-point-right,blue larger %}，正如文章结尾所述，该方法会将api key暴露在前端，尤其是在响应日志中：
{% asset_img f8f6d1a741d2caf68b72132cbc361dc4.png "图片描述" %}
   
&nbsp; &nbsp; &nbsp; 之前一直抱着侥幸心理，懒得去做更多的安全防护，直到上星期泄露的key被人大量使用（当时我的deepseek号里只有2块多钱，所以几乎没什么损失，一下就欠费停用了），才开始考虑这个安全问题。   
   
&nbsp; &nbsp; &nbsp; 最简单的方法就是给api上一个白名单功能，仅让一些域名能使用它。但是deepseek官方的api并不支持这个功能（不知道为什么，有的大模型提供商支持。那为什么不用别的？因为DS官方价格真的很便宜，极致的成本控制🤪）。   
   
&nbsp; &nbsp; &nbsp; 下面是改进版的方案，借助Cloudflare的workers，给deepseek的api加上白名单功能。对于前端而言，该方案的安全性是足够的：   
   
- Api key对访问者完全隐藏
- 访问者仅能知晓一个代理地址，并且拿到代理地址也无法使用

# Workers

&nbsp; &nbsp; &nbsp; 在[Cloudflare](https://dash.cloudflare.com)上新建一个workers项目，随意命名，例如，我的就是deepseek：
{% asset_img 97689fda6117530997faa65e7c75c2a9.png "图片描述" %}

随后修改代码，将以下内容填入，替换初始的hello world：   
   
```JS
const CONFIG = {
  // 你的 DeepSeek API Key
  DEEPSEEK_API_KEY: 'sk-xxxxxxxxxx',
  
  // DeepSeek API 端点
  DEEPSEEK_API_URL: 'https://api.deepseek.com/v1/chat/completions',
  
  // 允许的域名白名单
  ALLOWED_ORIGINS: [
    'https://yourdomain.com',
  //注意！仅在测试时添加localhost，否则key与完全暴露无异！
    'http://localhost:4000'
  ],
  
  // 速率限制配置 (可选)
  RATE_LIMIT: {
    enabled: true,
    maxRequests: 100,  // 每个 IP 每小时最多请求次数
    windowMs: 3600000  // 1小时
  }
};

// 速率限制存储 (使用 Workers KV 或内存)
const rateLimitMap = new Map();

addEventListener('fetch', event => {
  const request = event.request;
  
  // 处理 OPTIONS 预检请求
  if (request.method === 'OPTIONS') {
    event.respondWith(handleOptions(request));
  } else {
    event.respondWith(handleRequest(request));
  }
});

function handleOptions(request) {
  const origin = request.headers.get('Origin');
  
  // 验证来源
  if (origin && isOriginAllowed(origin, null)) {
    return new Response(null, {
      status: 204,
      headers: {
        'Access-Control-Allow-Origin': origin,
        'Access-Control-Allow-Methods': 'POST, OPTIONS',
        'Access-Control-Allow-Headers': 'Content-Type',
        'Access-Control-Max-Age': '86400'
      }
    });
  }
  
  return new Response(null, { status: 403 });
}

async function handleRequest(request) {
  // 只允许 POST 请求
  if (request.method !== 'POST') {
    return new Response('Method not allowed', { status: 405 });
  }

  // 获取请求来源
  const origin = request.headers.get('Origin');
  const referer = request.headers.get('Referer');
  
  console.log('Request from:', origin || referer); // 添加日志
  
  // 验证域名白名单
  if (!isOriginAllowed(origin, referer)) {
    console.log('Origin not allowed'); // 添加日志
    return new Response(JSON.stringify({
      error: 'Forbidden',
      message: 'Domain not in whitelist',
      origin: origin,
      referer: referer
    }), {
      status: 403,
      headers: { 
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': origin || '*'
      }
    });
  }

  // 速率限制检查
  const clientIP = request.headers.get('CF-Connecting-IP') || 'unknown';
  if (CONFIG.RATE_LIMIT.enabled && !checkRateLimit(clientIP)) {
    return new Response(JSON.stringify({
      error: 'Rate limit exceeded',
      message: 'Too many requests. Please try again later.'
    }), {
      status: 429,
      headers: { 
        'Content-Type': 'application/json',
        'Retry-After': '3600'
      }
    });
  }

  try {
    // 获取请求体
    const requestBody = await request.json();
    
    console.log('Request body:', requestBody); // 添加日志
    
    // 可选: 验证和限制请求参数
    if (!validateRequest(requestBody)) {
      return new Response(JSON.stringify({
        error: 'Invalid request',
        message: 'Request body validation failed'
      }), {
        status: 400,
        headers: { 
          'Content-Type': 'application/json',
          'Access-Control-Allow-Origin': origin || '*'
        }
      });
    }

    // 转发请求到 DeepSeek API
    const apiResponse = await fetch(CONFIG.DEEPSEEK_API_URL, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${CONFIG.DEEPSEEK_API_KEY}`
      },
      body: JSON.stringify(requestBody)
    });

    // 获取响应
    const responseData = await apiResponse.json();
    
    console.log('API Response status:', apiResponse.status); // 添加日志

    // 返回响应,添加 CORS 头
    return new Response(JSON.stringify(responseData), {
      status: apiResponse.status,
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': origin || '*',
        'Access-Control-Allow-Methods': 'POST, OPTIONS',
        'Access-Control-Allow-Headers': 'Content-Type',
        'Access-Control-Max-Age': '86400'
      }
    });

  } catch (error) {
    console.error('Worker error:', error); // 添加日志
    return new Response(JSON.stringify({
      error: 'Internal server error',
      message: error.message
    }), {
      status: 500,
      headers: { 
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': origin || '*'
      }
    });
  }
}

// 验证域名白名单
function isOriginAllowed(origin, referer) {
  // 检查 Origin 头
  if (origin && CONFIG.ALLOWED_ORIGINS.includes(origin)) {
    return true;
  }
  
  // 检查 Referer 头 (作为备选)
  if (referer) {
    try {
      const refererUrl = new URL(referer);
      const refererOrigin = `${refererUrl.protocol}//${refererUrl.host}`;
      if (CONFIG.ALLOWED_ORIGINS.includes(refererOrigin)) {
        return true;
      }
    } catch (e) {
      // Referer 解析失败
    }
  }
  
  return false;
}

// 速率限制检查
function checkRateLimit(clientIP) {
  const now = Date.now();
  const key = `rate_${clientIP}`;
  
  if (!rateLimitMap.has(key)) {
    rateLimitMap.set(key, { count: 1, resetTime: now + CONFIG.RATE_LIMIT.windowMs });
    return true;
  }
  
  const rateData = rateLimitMap.get(key);
  
  // 检查是否需要重置
  if (now > rateData.resetTime) {
    rateLimitMap.set(key, { count: 1, resetTime: now + CONFIG.RATE_LIMIT.windowMs });
    return true;
  }
  
  // 检查是否超过限制
  if (rateData.count >= CONFIG.RATE_LIMIT.maxRequests) {
    return false;
  }
  
  // 增加计数
  rateData.count++;
  return true;
}

// 验证请求体 (可选的安全检查)
function validateRequest(body) {
  // 检查必需字段
  if (!body.model || !body.messages) {
    return false;
  }
  
  // 限制 max_tokens (防止滥用)
  if (body.max_tokens && body.max_tokens > 4000) {
    body.max_tokens = 4000;
  }
  

  
  return true;
}


```
记得在代码开头配置好你的KEY，以及允许的域名白名单。

{% note warning modern %}
&nbsp; &nbsp; &nbsp; ⚠️⚠️⚠️注意！不要在白名单中加入http://localhost:4000 ！否则攻击者可以使用地址在本地调用API。除非进行测试，请不要加入该地址，并且在测试后即时将其删除！
{% endnote %} 

# 调用
&nbsp; &nbsp; &nbsp; 在博客端，更新`postai.js`文件,引用方法参见{% btn 'https://2am.top/2025/08/08/%E5%8D%9A%E5%AE%A2%E6%8E%A5%E5%85%A5deepseek%E6%91%98%E8%A6%81/',博客接入Deepseek摘要,far fa-hand-point-right,green larger %}，内容：
```JS
if (!window.hasOwnProperty("aiExecuted")) {
    console.log(`%cPost-Summary-AI 文章摘要AI生成工具,魔改自：%chttps://github.com/qxchuckle/Post-Summary-AI%c`, "border:1px #888 solid;border-right:0;border-radius:5px 0 0 5px;padding: 5px 10px;color:white;background:#4976f5;margin:10px 0", "border:1px #888 solid;border-left:0;border-radius:0 5px 5px 0;padding: 5px 10px;", "");
    window.aiExecuted = "chuckle";
}

function ChucklePostAI(AI_option) {
    MAIN(AI_option);

    if (AI_option.pjax) {
        document.addEventListener('pjax:complete', () => {
            setTimeout(() => {
                MAIN(AI_option);
            }, 0);
        });
    }

    function MAIN(AI_option) {
        // 如果有则删除
        const box = document.querySelector(".post-ai");
        if (box) {
            box.parentElement.removeChild(box);
        }

        const currentURL = window.location.href;

        // 排除页面检查
        if (AI_option.eliminate && AI_option.eliminate.length && AI_option.eliminate.some(item => currentURL.includes(item))) {
            console.log("Post-Summary-AI 已排除当前页面(黑名单)");
            return;
        }
        if (AI_option.whitelist && AI_option.whitelist.length && !AI_option.whitelist.some(item => currentURL.includes(item))) {
            console.log("Post-Summary-AI 已排除当前页面(白名单)");
            return;
        }

        // 获取挂载元素
        let targetElement = "";
        if (!AI_option.auto_mount && AI_option.el) {
            targetElement = document.querySelector(AI_option.el ? AI_option.el : '#post #article-container');
        } else {
            targetElement = getArticleElements();
        }

        // 获取文章标题
        const post_title = document.querySelector(AI_option.title_el) ? document.querySelector(AI_option.title_el).textContent : document.title;

        if (!targetElement) {
            return;
        };

        const interface = {
            name: "然-AI",
            introduce: "我是文章辅助AI: 然-AI，一个基于deepseek的强大语言模型，有什么可以帮到您？😊",
            version: "deepseek",
            button: ["介绍自己😎", "来点灵感💡", "生成AI简介🤖"],
            ...AI_option.interface
        }

        insertCSS(); // 插入css

        // 插入html结构
        const post_ai_box = document.createElement('div');
        post_ai_box.className = 'post-ai';
        post_ai_box.setAttribute('id', 'post-ai');
        targetElement.insertBefore(post_ai_box, targetElement.firstChild);

        post_ai_box.innerHTML = `<div class="ai-title">
        <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="21px" height="21px" viewBox="0 0 48 48">
        <g id="&#x673A;&#x5668;&#x4EBA;" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd"><path d="M34.717885,5.03561087 C36.12744,5.27055371 37.079755,6.60373651 36.84481,8.0132786 L35.7944,14.3153359 L38.375,14.3153359 C43.138415,14.3153359 47,18.1768855 47,22.9402569 L47,34.4401516 C47,39.203523 43.138415,43.0650727 38.375,43.0650727 L9.625,43.0650727 C4.861585,43.0650727 1,39.203523 1,34.4401516 L1,22.9402569 C1,18.1768855 4.861585,14.3153359 9.625,14.3153359 L12.2056,14.3153359 L11.15519,8.0132786 C10.920245,6.60373651 11.87256,5.27055371 13.282115,5.03561087 C14.69167,4.80066802 16.024865,5.7529743 16.25981,7.16251639 L17.40981,14.0624532 C17.423955,14.1470924 17.43373,14.2315017 17.43948,14.3153359 L30.56052,14.3153359 C30.56627,14.2313867 30.576045,14.1470924 30.59019,14.0624532 L31.74019,7.16251639 C31.975135,5.7529743 33.30833,4.80066802 34.717885,5.03561087 Z M38.375,19.4902885 L9.625,19.4902885 C7.719565,19.4902885 6.175,21.0348394 6.175,22.9402569 L6.175,34.4401516 C6.175,36.3455692 7.719565,37.89012 9.625,37.89012 L38.375,37.89012 C40.280435,37.89012 41.825,36.3455692 41.825,34.4401516 L41.825,22.9402569 C41.825,21.0348394 40.280435,19.4902885 38.375,19.4902885 Z M14.8575,23.802749 C16.28649,23.802749 17.445,24.9612484 17.445,26.3902253 L17.445,28.6902043 C17.445,30.1191812 16.28649,31.2776806 14.8575,31.2776806 C13.42851,31.2776806 12.27,30.1191812 12.27,28.6902043 L12.27,26.3902253 C12.27,24.9612484 13.42851,23.802749 14.8575,23.802749 Z M33.1425,23.802749 C34.57149,23.802749 35.73,24.9612484 35.73,26.3902253 L35.73,28.6902043 C35.73,30.1191812 34.57149,31.2776806 33.1425,31.2776806 C31.71351,31.2776806 30.555,30.1191812 30.555,28.6902043 L30.555,26.3902253 C30.555,24.9612484 31.71351,23.802749 33.1425,23.802749 Z" id="&#x5F62;&#x72B6;&#x7ED3;&#x5408;" fill="#444444" fill-rule="nonzero"></path></g></svg>
        <div class="ai-title-text">${interface.name}</div>
        <div class="ai-tag">${interface.version}</div>
      </div>
      <div class="ai-explanation">${interface.name}初始化中...</div>
      <div class="ai-btn-box">
        <div class="ai-btn-item">${interface.button[0]}</div>
        <div class="ai-btn-item">${interface.button[1]}</div>
        <div class="ai-btn-item">${interface.button[2]}</div>
      </div>`;

        // AI主体业务逻辑
        let animationRunning = true;
        let explanation = document.querySelector('.ai-explanation');
        let post_ai = document.querySelector('.post-ai');
        let ai_btn_item = document.querySelectorAll('.ai-btn-item');
        let ai_str = '';
        let ai_str_length = '';
        let delay_init = 600;
        let i = 0;
        let j = 0;
        let speed = AI_option.speed || 20;
        let character_speed = speed * 7.5;
        let sto = [];
        let elapsed = 0;
        let completeGenerate = false;
        let controller = new AbortController();
        let signal = controller.signal;


        // 打字机动画
        const animate = (timestamp) => {
            if (!animationRunning) {
                return;
            }
            if (!animate.start) animate.start = timestamp;
            elapsed = timestamp - animate.start;
            if (elapsed >= speed) {
                animate.start = timestamp;
                if (i < ai_str_length - 1) {
                    let char = ai_str.charAt(i + 1);
                    let delay = /[,.，。!?！？]/.test(char) ? character_speed : speed;
                    if (explanation.firstElementChild) {
                        explanation.removeChild(explanation.firstElementChild);
                    }
                    explanation.innerHTML += char;
                    let div = document.createElement('div');
                    div.className = "ai-cursor";
                    explanation.appendChild(div);
                    i++;
                    if (delay === character_speed) {
                        document.querySelector('.ai-explanation .ai-cursor').style.opacity = "0";
                    }
                    if (i === ai_str_length - 1) {
                        observer.disconnect();
                        explanation.removeChild(explanation.firstElementChild);
                    }
                    sto[0] = setTimeout(() => {
                        requestAnimationFrame(animate);
                    }, delay);
                }
            } else {
                requestAnimationFrame(animate);
            }
        };

        const observer = new IntersectionObserver((entries) => {
            let isVisible = entries[0].isIntersecting;
            animationRunning = isVisible;
            if (animationRunning) {
                delay_init = i === 0 ? 200 : 20;
                sto[1] = setTimeout(() => {
                    if (j) {
                        i = 0;
                        j = 0;
                    }
                    if (i === 0) {
                        explanation.innerHTML = ai_str.charAt(0);
                    }
                    requestAnimationFrame(animate);
                }, delay_init);
            }
        }, { threshold: 0 });

        function clearSTO() {
            if (sto.length) {
                sto.forEach((item) => {
                    if (item) {
                        clearTimeout(item);
                    }
                });
            }
        }

        function resetAI(df = true, str = '生成中. . .') {
            i = 0;
            j = 1;
            clearSTO();
            animationRunning = false;
            elapsed = 0;
            if (df) {
                explanation.innerHTML = str;
            } else {
                explanation.innerHTML = '请等待. . .';
            }
            if (!completeGenerate) {
                controller.abort();
            }
            ai_str = '';
            ai_str_length = '';
            observer.disconnect();
        }

        function startAI(str, df = true) {
            if (AI_option.hasOwnProperty('typewriter') && !AI_option.typewriter) {
                explanation.innerHTML = str;
            } else {
                resetAI(df);
                ai_str = str;
                ai_str_length = ai_str.length;
                observer.observe(post_ai);
            }
        }

        function aiIntroduce() {
            startAI(interface.introduce);
        }

        // 新的灵感生成功能
        async function aiInspiration() {
            resetAI();
            const response = await getAIResponse("你是一个灵感发生器，给用户提供有意思的灵感，不要超过100字，不要分段，不要分点，不要换行");
            if (response) {
                startAI(response);
            }
        }

        async function aiGenerateAbstract() {
            resetAI();
            const ele = targetElement;
            const content = getTextContent(ele);
            // 添加调试日志
            console.log("获取到的文章内容:", content.substring(0, 200) + "...");
            // 优化提示词，确保AI理解需要处理的是文章内容
            const prompt = `请根据以下文章内容生成一个简洁的摘要，不要超过200字，不要换行，不要提出建议或评论，只需总结文章主要内容。文章标题和内容如下：${content}`;
            const response = await getAIResponse(prompt);
            if (response) {
                startAI(response);
            }
        }


        // 统一的AI响应函数
        async function getAIResponse(prompt) {
            completeGenerate = false;
            controller = new AbortController();
            signal = controller.signal;

            const apiUrl = "https://deepseek.xxx.workers.dev";

            try {
                const response = await fetch(apiUrl, {
                    signal: signal,
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify({
                        model: "deepseek-chat",
                        messages: [{ "role": "user", "content": prompt }],
                    })
                });

                completeGenerate = true;

                if (response.status === 429) {
                    startAI('请求过于频繁，请稍后再请求AI。');
                    return null;
                }

                if (!response.ok) {
                    throw new Error('Response not ok');
                }

                const data = await response.json();
                return data.choices[0].message.content;
            } catch (error) {
                if (error.name === "AbortError") {
                    // 请求被中止
                } else {
                    console.error('Error occurred:', error);
                    startAI(`${interface.name}请求AI出错了，请稍后再试。`);
                }
                completeGenerate = true;
                return null;
            }
        }

        // 获取文章内容
        function getTextContent(element) {
            const totalLength = AI_option.total_length || 3000; // 增加默认长度限制
            // 获取完整的文章内容，不再截取
            const content = `文章标题：${post_title}。文章内容：${getText(element)}`;
            return content;
        }

        // 提取纯文本
        function getText(element) {
            // 添加调试日志
            console.log("开始提取文章内容，元素:", element);

            const excludeClasses = AI_option.exclude ? AI_option.exclude : ['highlight', 'Copyright-Notice', 'post-ai', 'post-series'];
            if (!excludeClasses.includes('post-ai')) { excludeClasses.push('post-ai'); }
            const excludeTags = ['script', 'style', 'iframe', 'embed', 'video', 'audio', 'img', 'svg'];

            let textContent = '';

            // 尝试使用innerText作为备选方案，它比递归遍历更可靠
            if (element.innerText) {
                textContent = element.innerText;
                console.log("使用innerText获取的内容长度:", textContent.length);
            } else {
                // 原有递归方法作为备选
                for (let node of element.childNodes) {
                    if (node.nodeType === Node.TEXT_NODE) {
                        textContent += node.textContent.trim();
                    } else if (node.nodeType === Node.ELEMENT_NODE) {
                        let hasExcludeClass = false;
                        for (let className of node.classList) {
                            if (excludeClasses.includes(className)) {
                                hasExcludeClass = true;
                                break;
                            }
                        }
                        let hasExcludeTag = excludeTags.includes(node.tagName.toLowerCase());
                        if (!hasExcludeClass && !hasExcludeTag) {
                            let innerTextContent = getText(node);
                            textContent += innerTextContent;
                        }
                    }
                }
                console.log("使用递归方法获取的内容长度:", textContent.length);
            }

            // 清理文本内容
            textContent = textContent.replace(/\s+/g, ' ').trim();

            // 如果内容太短，可能是提取失败，尝试使用更通用的选择器
            if (textContent.length < 100) {
                console.log("提取的内容可能不完整，尝试使用备选方法");
                const commonSelectors = ['.post-content', '.entry-content', '.content', 'article', '#article-container', '.article-content', '.post-body'];
                for (let selector of commonSelectors) {
                    const fallbackElement = document.querySelector(selector);
                    if (fallbackElement && fallbackElement !== element) {
                        console.log("尝试使用备选选择器:", selector);
                        const fallbackContent = fallbackElement.innerText || getText(fallbackElement);
                        if (fallbackContent.length > textContent.length) {
                            textContent = fallbackContent;
                            console.log("使用备选选择器获取到更长的内容");
                            break;
                        }
                    }
                }
            }

            console.log("最终提取的内容长度:", textContent.length);
            return textContent;
        }

        // 按比例切割字符串
        function extractString(str, totalLength = 1000) {
            totalLength = Math.min(totalLength, 5000);
            if (str.length <= totalLength) { return str; }

            const firstPart = str.substring(0, Math.floor(totalLength * 0.5));
            const lastPart = str.substring(str.length - Math.floor(totalLength * 0.5));
            return firstPart + lastPart;
        }

        // 自动获取文章内容容器
        function getArticleElements() {
            // 扩展选择器列表，增加更多可能的选择器
            const selectors = [
                'article', 
                '.post-content', 
                '.entry-content', 
                '.content', 
                '#article-container',
                '.article-content',
                '.post-body',
                '.post-entry',
                '.main-content',
                '.article',
                '.post',
                '.blog-post',
                '.post-text',
                '.article-body'
            ];

            // 添加调试日志
            console.log("尝试查找文章内容容器...");

            for (let selector of selectors) {
                const element = document.querySelector(selector);
                if (element) {
                    console.log("找到文章内容容器:", selector);
                    // 检查元素是否有足够的内容
                    const textLength = element.innerText ? element.innerText.length : 0;
                    console.log("元素内容长度:", textLength);

                    // 如果内容太少，继续寻找
                    if (textLength > 100) {
                        return element;
                    }
                }
            }

            // 如果没有找到合适的选择器，尝试查找包含最大文本量的元素
            console.log("未找到合适的文章内容容器，尝试查找包含最多文本的元素...");
            const allElements = document.querySelectorAll('div, section, main, article');
            let maxTextElement = null;
            let maxTextLength = 0;

            for (let element of allElements) {
                // 排除导航、页脚等元素
                const classList = element.className || '';
                const id = element.id || '';
                if (classList.includes('nav') || classList.includes('menu') || 
                    classList.includes('sidebar') || classList.includes('footer') ||
                    classList.includes('header') || classList.includes('comment') ||
                    id.includes('nav') || id.includes('menu') || 
                    id.includes('sidebar') || id.includes('footer') ||
                    id.includes('header') || id.includes('comment')) {
                    continue;
                }

                const textLength = element.innerText ? element.innerText.length : 0;
                if (textLength > maxTextLength && textLength > 200) {
                    maxTextLength = textLength;
                    maxTextElement = element;
                }
            }

            if (maxTextElement) {
                console.log("找到包含最多文本的元素，内容长度:", maxTextLength);
                return maxTextElement;
            }

            console.log("未找到合适的文章内容容器，使用document.body");
            return document.body;
        }

        // 插入CSS
        function insertCSS() {
            const styleId = 'qx-ai-style';
            if (document.getElementById(styleId)) { return; }
            const styleElement = document.createElement('style');
            styleElement.id = styleId;
            styleElement.textContent = AI_option.css || `:root{--ai-font-color:#353535;--ai-post-bg:#f1f3f8;--ai-content-bg:#fff;--ai-content-border:1px solid #e3e8f7;--ai-border:1px solid #e3e8f7bd;--ai-tag-bg:rgba(48,52,63,0.80);--ai-cursor:#333;--ai-btn-bg:rgba(48,52,63,0.75);--ai-title-color:#4c4948;--ai-btn-color:#fff;}[data-theme=dark],.theme-dark,body.dark,body.dark-theme{--ai-font-color:rgba(255,255,255,0.9);--ai-post-bg:#30343f;--ai-content-bg:#1d1e22;--ai-content-border:1px solid #42444a;--ai-border:1px solid #3d3d3f;--ai-tag-bg:#1d1e22;--ai-cursor:rgb(255,255,255,0.9);--ai-btn-bg:#1d1e22;--ai-title-color:rgba(255,255,255,0.86);--ai-btn-color:rgb(255,255,255,0.9);}#post-ai.post-ai{background:var(--ai-post-bg);border-radius:12px;padding:10px 12px 11px;line-height:1.3;border:var(--ai-border);margin-top:10px;margin-bottom:6px;transition:all 0.3s;}#post-ai .ai-title{display:flex;color:var(--ai-title-color);border-radius:8px;align-items:center;padding:0 6px;position:relative;}#post-ai .ai-title-text{font-weight:bold;margin-left:8px;font-size:17px;}#post-ai .ai-tag{font-size:12px;background-color:var(--ai-tag-bg);color:var(--ai-btn-color);border-radius:4px;margin-left:auto;line-height:1;padding:4px 5px;border:var(--ai-border);}#post-ai .ai-explanation{margin-top:10px;padding:8px 12px;background:var(--ai-content-bg);border-radius:8px;border:var(--ai-content-border);font-size:18px;line-height:1.4;color:var(--ai-font-color);}#post-ai .ai-cursor{display:inline-block;width:7px;background:var(--ai-cursor);height:16px;margin-bottom:-2px;opacity:0.95;margin-left:3px;transition:all 0.3s;}#post-ai .ai-btn-box{font-size:15.5px;width:100%;display:flex;flex-direction:row;flex-wrap:wrap;}#post-ai .ai-btn-item{padding:5px 10px;margin:10px 16px 0px 5px;width:fit-content;line-height:1;background:var(--ai-btn-bg);border:var(--ai-border);color:var(--ai-btn-color);border-radius:6px 6px 6px 0;user-select:none;transition:all 0.3s;cursor:pointer;}#post-ai .ai-btn-item:hover{background:#49b0f5dc;}@media screen and (max-width:768px){#post-ai .ai-btn-box{justify-content:center;}}#post-ai .ai-title>svg{width:21px;height:fit-content;}#post-ai .ai-title>svg path{fill:var(--ai-font-color);}`;
            AI_option.additional_css && (styleElement.textContent += AI_option.additional_css);
            document.head.appendChild(styleElement);
        }

        // AI初始化，绑定按钮事件
        async function ai_init() {
            explanation = document.querySelector('.ai-explanation');
            post_ai = document.querySelector('.post-ai');
            ai_btn_item = document.querySelectorAll('.ai-btn-item');

            const funArr = [aiIntroduce, aiInspiration, aiGenerateAbstract];

            ai_btn_item.forEach((item, index) => {
                item.addEventListener('click', () => {
                    funArr[index]();
                });
            });

            if (AI_option.summary_directly) {
                aiGenerateAbstract();
            } else {
                aiIntroduce();
            }
        }

        ai_init();
    }
}

// 兼容旧版本配置项
if (typeof ai_option !== "undefined") {
    console.log("正在使用旧版本配置方式，请前往项目仓库查看最新配置写法");
    new ChucklePostAI(ai_option);
}
```
找到这行
```JS
const apiUrl = "https://deepseek.xxx.workers.dev";
```
将地址替换为你的workers地址。

# 加速
&nbsp; &nbsp; &nbsp; 如果使用默认的workers地址，形如`.workers.dev`，那么大概率会遇到连接超时的问题（连梯子倒是能解决），解决方法是给workers绑定一个自定义域名，并且是解析在cloudflare上的，使用一个子域名就行。   
   
&nbsp; &nbsp; &nbsp; 例如，在cloudflare上解析/加速/代理了一个域名为example.com，那么给workers绑定一个deepseek.example.com即可。

# 总结
&nbsp; &nbsp; &nbsp; 这种方法的原理很简单：在前端和Deepseek之间加一个**中间件**（或者称之为**过滤器**），让workers处理所有来自在白名单中的请求，转发来自Deepseek的响应，并且屏蔽所有非白名单的请求。   
   
&nbsp; &nbsp; &nbsp; 实际上，这种方法不仅可以用在这个场景，之后如果遇到类似的情况（比如其他的什么API，或者某个暴露的服务端口，必须暴露但又需要控制访问），都可以使用workers来代理流量。
