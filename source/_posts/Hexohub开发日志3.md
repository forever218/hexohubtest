---
title: Hexohub开发日志3
date: 2025-09-20 20:47:01
tags:
  - 技术
  - 前端
  - 后端
  - hexo
  - blog
  - Hexo
  - 开发日志
cover: 0.png
background: url(0.png)
---
{% note info modern %}
[HexoHub](https://github.com/forever218/HexoHub)是款个人开发的项目（桌面应用程序），旨在提供一个一体化的hexo集成可视化面板，优化hexo使用体验。
{% endnote %}   

&nbsp; &nbsp; &nbsp; 截至撰文，[HexoHub](https://github.com/forever218/HexoHub)已更新到[v2.6.0](https://github.com/forever218/HexoHub/releases/tag/v2.6.0)。2.6.0版本主要有以下更改：  
1. 提供了服务器预览的模式选项，可以在预览中直接看到最终的网页效果   
2. 修改了推送命令，有望解决某些情况下推送失败的问题   
3. 在启动服务器前，自动关闭当前正在占用4000端口进程的应用程序（包括hexo自身）   

# 预览
&nbsp; &nbsp; &nbsp; 在2.6.0版本之前，程序一直使用`react`系列的组件来渲染预览内容，下面是一些核心组件：  
``` json
    "react-markdown": "^10.1.0",
    "react-resizable-panels": "^3.0.3",
    "react-syntax-highlighter": "^15.6.1",
    "recharts": "^3.1.2",
    "rehype-katex": "^7.0.1",
    "remark-breaks": "^4.0.0",
    "remark-gfm": "^4.0.1",
    "remark-math": "^6.0.0",
```
&nbsp; &nbsp; &nbsp;  这种渲染方式对常见的markdown文件很有效，能渲染出github风格的文档，但是对于hexo系统下的文章效果并不好。因为hexo允许用户在文章中使用大量的标签外挂，例如`{% asset_img %}`标签，butterfly主题下的`note`标签等等，这些都不是标准的markdown语法，react并不认识，只会将他们渲染为文本。
&nbsp; &nbsp; &nbsp;  在很长一段时间内，都在思考如何解决这个问题，尤其是对`{% asset_img %}`标签，图片显示不出来很影响观感，往往不知道这里放的是哪张图片。一开始，我的思路是：添加自定义规则，先遍历文章，找到所有的`{% asset_img %}`标签，将他们独立渲染为`![xxx](xxx)`这样的标准md图片语法，再将这些参数传递给react组件，完成最终的渲染。我认为这个方式是相当可行的，可是后来无论如何修改图片地址的获取方式，甚至从盘符开始拼接`![](file:////D://xxxxxx）`始终是显示图片碎裂的标志，在控制台又没有看见任何报错。在这个方法上折腾了相当久，我也尝试过绕过react组件，将检测到的`{% asset_img %}`标签直接用自定义规则渲染为`<src img=xxxxx>`这样的html格式，依旧无法显示。这个问题到现在都没解决，我认为极大可能是与electron应用对本地文件访问的权限有关。   
&nbsp; &nbsp; &nbsp;  后来又想到，就算组件实现了`{% asset_img %}`标签的渲染，那`note`这样的标签呢，这类第三方标签外挂无穷无尽，不可能每一个都写一个对应的规则上去。想想就头疼，于是项目搁置了一段时间。  

## 转折
&nbsp; &nbsp; &nbsp;  今天闲着没事翻阅自己以前的博客，突然间看到一篇名为[套娃页面](https://2am.top/2022/12/18/%E5%A5%97%E5%A8%83%E9%A1%B5%E9%9D%A2/)的文章，一下就眼前一亮，我靠，`iframe`元素！既然自己渲染不出来，为什么不用hexo渲染好的结果呢？！自己之前真的是一个大笨逼，逮着react死磕。
&nbsp; &nbsp; &nbsp;  思路一下就清晰了：在预览的时候启动hexo服务器，预览框内直接从`localhost:4000`开始，然后从当前正在编辑的markdown文件读取`date`和`title`，将这三部分拼接为一个完整的地址，传递给`iframe`，如此一来就能在预览框内查看最终网页版的结果了。  

{% asset_img 12db3038a6aad24517219b2d180eac27.png "图片描述" %}


## 使用
&nbsp; &nbsp; &nbsp;  要使用服务器预览功能，请到面板设置里打开“服务器预览”。当您打开此选项后，再点击“启动服务器”，就不会弹出浏览器窗口了，一切都在程序内查看（您依旧可以在浏览器里访问localhost:4000）。

{% asset_img 76191ea26ffb950d073a19bcc73ff65a.png "图片描述" %}

&nbsp; &nbsp; &nbsp;  **请注意**，服务器预览的地址获取是标准的hexo格式：“localhost:4000/年/月/日/文章名”，如果您有使用第三方插件来改变hexo原生的地址生成方式，那么可能会导致无法正确显示。此时，您应该选择**根地址**，这样返回的就是“localhost:4000”，即您的博客主页，您只需要点击对应的文章即可预览。由于插件工作方式各异，生成的地址也不尽相同，所以请原谅我没有做更多的适配。

{% asset_img 8bd6b05ba3fbfac0bc20d369e38d927a.png "图片描述" %}

## 刷新
&nbsp; &nbsp; &nbsp;  您不需要任何操作，每当您保存文章（无论是自动保存还是手动保存），都会触发服务器的强制刷新（模拟ctl+f5），确保不会有缓存的干扰。

## 已知问题
&nbsp; &nbsp; &nbsp;  触发刷新后，预览框会返回顶部，而不是返回刷新前的位置。这个问题我拼尽全力无法解决。本来应该是很简单的，只需要添加一个脚本就能完成，但也许是在electron环境下，问题依旧存在。
``` js
// 在iframe内部的页面中添加以下代码
(function() {
    const SCROLL_KEY = 'iframe_scroll_position';
    
    // 页面加载时恢复滚动位置
    window.addEventListener('load', function() {
        const savedPosition = sessionStorage.getItem(SCROLL_KEY);
        if (savedPosition) {
            window.scrollTo(0, parseInt(savedPosition));
        }
    });
    
    // 监听滚动事件，保存位置
    let scrollTimer;
    window.addEventListener('scroll', function() {
        clearTimeout(scrollTimer);
        scrollTimer = setTimeout(function() {
            sessionStorage.setItem(SCROLL_KEY, window.pageYOffset.toString());
        }, 100);
    });
    
    // 页面卸载时保存位置
    window.addEventListener('beforeunload', function() {
        sessionStorage.setItem(SCROLL_KEY, window.pageYOffset.toString());
    });
})();
```
&nbsp; &nbsp; &nbsp;  另外，可能偶尔会出现图片不渲染的情况。这时请先关闭服务器，点击上方的`清理`，随后再启动服务器即可。

# 端口
&nbsp; &nbsp; &nbsp;  其实从2.3.x版本开始，就已经有端口关闭的功能，以防止前面的hexo服务器残留，占用4000端口导致服务器启动失败，不过效果似乎不好，服务器还是偶发的有残留。在2.6.0中加入了关闭进程的功能：
```js
   // 尝试杀死占用4000端口的进程
    const { exec } = require('child_process');
    if (WindowsCompat.isWindows()) {
      exec('netstat -ano | findstr :4000', (error, stdout, stderr) => {
        if (!error) {
          const lines = stdout.split('\n');
          for (const line of lines) {
            if (line.includes(':4000') && line.includes('LISTENING')) {
              const parts = line.trim().split(/\s+/);
              const pid = parts[parts.length - 1];
              if (pid && !isNaN(parseInt(pid))) {
                exec(`taskkill /F /PID ${pid}`, (killError) => {
                  if (killError) {
                  } else {
                  }
                });
              }
            }
          }
        }
      });
```
现在当您点击启动服务器时，程序会尝试kill掉所有正在占用4000端口的进程。

# 推送
&nbsp; &nbsp; &nbsp;  之前的推送功能有些问题，会导致推送失败。更多详情请参见[#issues6](https://github.com/forever218/HexoHub/issues/6)，更改请参见[更改日志](https://github.com/forever218/HexoHub/commit/1c238004d539266268611cf4719ca2db6922e03a)。主要改变了命令内容，在执行git命令时使用`-C`来进入用户选择的hexo根目录下，例子：
``` bash
const addResult = await ipcRenderer.invoke('execute-command', 'git add .', hexoPath);  //更改前  
const addResult = await ipcRenderer.invoke('execute-command', `git -c "${hexoPath}" add .`);  //更改后
```
&nbsp; &nbsp; &nbsp;  感谢[@kemiaofxjun](https://github.com/kemiaofxjun)提出的issue，让我发现了更多的漏洞：事实上，因为每个人电脑上的git环境和配置都不一样，有的在hexo下已经初始化了git，有的还没有等等等等。自己之前在做这个功能的时候考虑的太简单了，没有考虑实际应用的情况，所以推送这个功能您可以当作是测试功能来使用，如果推送失败，请用传统的命令行来执行。日后我会继续完善推送功能。

# 自言自语
&nbsp; &nbsp; &nbsp;  最近忙着秋招的东西，前天的时候参加了一个线上全栈工程师的笔试，考的一塌糊涂哈哈哈哈哈哈，算法题三道大题只写出来一道，选择题连猜带蒙，简答题都不知道自己写了什么。大厂的笔试还是太权威了，迟点我专门写一篇文章，来分享一下全栈的经历。
&nbsp; &nbsp; &nbsp;  一转眼hexohub距离上个版本发布过去了一个星期，还有好多建议和反馈没有处理，咕咕咕......时间真的不够。所以很抱歉，好多反馈不能即时处理，之后有空了，一定会挨个完善的。~~先画饼哈哈哈~~
