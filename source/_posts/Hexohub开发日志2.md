---
title: Hexohub开发日志2
date: 2025-09-13 17:36:22
tags:
  - 技术
  - 前端
  - 后端
  - hexo
  - blog
  - Hexo
  - 开发日志
cover: 0.jpg
background: url(0.jpg)
---
{% note info modern %}
[HexoHub](https://github.com/forever218/HexoHub)是款个人开发的项目（桌面应用程序），旨在提供一个一体化的hexo集成可视化面板，优化hexo使用体验。
{% endnote %}   

&nbsp; &nbsp; &nbsp; 截至撰文，[HexoHub](https://github.com/forever218/HexoHub)已更新到[v2.5.0](https://github.com/forever218/HexoHub/releases/tag/v2.5.0)。近几个版本的更新主要集中在新功能的加入和界面的优化，以下是具体的变动：   
 1.  预览中加入对数学公式的支持latex  
 2.  重构了背景的渲染逻辑，优化面板透明度调节体验，现在字体不会受到透明度调节的影响  
 3.  加入推送功能，配置相关选项后，可以一键将hexo项目推送至github     
 4.  预览时隐藏文章的front matter  
 5.  进一步优化程序性能，减小内存开销  
 6.  引入AI功能：灵感生成和文章分析  

# 推送
&nbsp; &nbsp; &nbsp; 要使用此功能，请先在面板设置中打开“推送”，顶部就会出现一个推送按钮，，随后填入相关内容，即可一键推送。请注意，您需要先使用ssh密钥与github取得联系。  

{% asset_img 86aee0724d5417cf20e10ff65af9d6a1.png "图片描述" %}

# AI功能
## 基础使用
&nbsp; &nbsp; &nbsp; 要使用此功能，请先在面板设置中打开“AI”，随后填入api key，目前仅支持deepseek。开启后，两个地方将出现AI支持：   

{% asset_img f59027cbc96fe2a0d52dc98da7f8dbf4.png "图片描述" %}

顶部的“来点灵感”，当您对写作没什么头绪时，可以点击“来点灵感”，生成一些创意来辅助创作。   

{% asset_img ccbce63f9fed7d272565cd0df1046eba.png "图片描述" %}

文章统计模块下的“文章分析”，点击“开始分析”来获取建议和提示。   

{% asset_img f8ac49021d36be06a0ab5fd45c00628a.png "图片描述" %}

## api key
&nbsp; &nbsp; &nbsp;  到{% btn 'https://platform.deepseek.com/',🔗deepseek开放平台,far fa-hand-point-right,blue larger %}注册/登录，选择`api key`-`创建api key`，随后复制您的密钥，将其填入hexohub即可。  

{% asset_img 58f3077899fda4cabd50dcf1805ef13d.png "图片描述" %}

## 提示词
&nbsp; &nbsp; &nbsp;  您可以在面板设置界面自定义提示词。灵感提示词默认为“你是一个灵感提示机器人，我是一个独立博客的博主，我想写一篇博客，请你给我一个可写内容的灵感，不要超过200字，不要分段”，分析提示词默认为“你是一个文章分析机器人，以下是我的博客数据{content}，请你分析并给出鼓励性的话语，不要超过200字，不要分段”。     
&nbsp; &nbsp; &nbsp;  请注意，对于分析提示词，`{content}`是必需的。无论您如何修改提示词，都应该包含`{content}`这个关键参数。开始分析后，程序会收集您的`标签`和`文章发布时间统计`数据，将其封装进`{content}`，发送给AI。 

## 安全性
&nbsp; &nbsp; &nbsp;  Hexohub将您的数据安全和隐私放在首位，不会以任何理由、任何形式来收集API KEY，您填入的API KEY只会保存在本地，不会向任何外界发送。您可以到[HexoHub开源仓库](https://github.com/forever218/HexoHub)查看相关代码以验证安全性。  
&nbsp; &nbsp; &nbsp;  出于安全考虑，当填入API KEY之后，内容将被“*********”覆盖，处于一个无法读取的状态，请您妥善保管好您的API KEY。  

## 模型  
&nbsp; &nbsp; &nbsp;  当前使用的是`deepseek-chat`模型
```tsx
      const response = await fetch('https://api.deepseek.com/v1/chat/completions', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${apiKey}`
        },
        body: JSON.stringify({
          model: 'deepseek-chat',
          messages: [
            {
              role: 'user',
              content: finalPrompt
            }
          ],
          temperature: 0.7,
          max_tokens: 500
        })
      });
```
为什么不是`deep-reasoner`？deepseek发布v3.1系列之后，chat和reasoner调用价格是一样的（其实我还挺意外的，深度思考的模型居然跟普通的chat是一个价，以前reasoner的价格是chat的两倍）。考虑到reasoner思考过程相对较长，而且使用场景也不需要多精确，所以还是使用chat。
## 定价
&nbsp; &nbsp; &nbsp;  您可以到{% btn 'https://api-docs.deepseek.com/zh-cn/quick_start/pricing/',🔗deepseek官方文档,far fa-hand-point-right,blue larger %}查看具体的模型调用价格。   
截至2025.9.13：

| 输入/价格 | deepseek-chat |
|-----|-----|
| 百万tokens输入（缓存命中） | 0.5元 |
| 百万tokens输入（缓存未命中） | 4元 |
| 百万tokens输出 | 12元 |

&nbsp; &nbsp; &nbsp;  在本应用程序的使用中，绝大多数都属于`缓存命中`的情况。  
&nbsp; &nbsp; &nbsp;  这么看可能不是很直观，举个具体的例子，9月13号，我一共使用了9次AI提示（灵感和分析加起来，均使用默认的提示词）。   

{% asset_img 6df8fc5e9d432c292bfa6b98f3a9ab8e.png "图片描述" %}

&nbsp; &nbsp; &nbsp;  一共消耗`4506 tokens`。  

{% asset_img 356b56723dad3a9a6f3f1487186013a5.png "图片描述" %}

&nbsp; &nbsp; &nbsp;  花费`0.01元`。实际上远不到1分钱，只不过计费的最小单位是1分钱而已。  

{% asset_img b589f9f9c9550a03195f787ee0de83c5.png "图片描述" %}

&nbsp; &nbsp; &nbsp;  具体的账单，您可以到{% btn 'https://platform.deepseek.com/',🔗deepseek开放平台,far fa-hand-point-right,blue larger %}查询。请注意，消耗的tokens数量与您的hexo项目和设置的提示词有关，建议还是在提示词里加上字数限制，以此来控制成本。

# 自言自语
&nbsp; &nbsp; &nbsp; hexohub首个发行版发布过去将近一个月了，觉得自己的收获相当大，文档编写、版本控制和管理、issues处理、反馈处理......学到了很多东西，远不止代码上的进步。一开始的时候，想着有一些hexo基本的功能就够了，比如清理、生成、推送之类的，说到底就是不想老是输入hexo xxxx这样的命令，所以在首个发行版之前，有过好几个“样品”，都没有现在这样的编辑窗口，没有卡片式的UI，就只有几个hexo命令的按钮，现在看来相当的简陋。   
&nbsp; &nbsp; &nbsp; 后来才想到，既然都这样了，为什么不再进一步呢？于是就把自己平时常用的功能加进去，比如说对`{%asst_img%}`标签的快捷方式。就这样加加加，把一些美观实用的设计都融进hexohub里，有了现在的模样。   
&nbsp; &nbsp; &nbsp; 很感谢为hexohub提出建议的朋友，每次看到一些建议，我都会想：我靠，是哦，确实存在这个问题，自己用的时候怎么没发现，加上之后确实方便多了，这些建议都在让这个作品变得更好。奈何自己能力有限，很多改进不能马上实现，感觉自己总是当鸽子。但我对博客的热爱始终没改变（2020.8.4发布首篇文章），还是会慢慢优化hexohub，就像林肯说的：   

> 我可以走的很慢，但绝不会停下脚步