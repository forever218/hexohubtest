---
title: Microsoft发布应用
date: 2025-11-21 21:53:26
tags:
  - 技术
  - hexo
  - blog
  - Hexo
  - 开发日志
cover: 0.jpg
background: url(0.jpg)
---
{% note info modern %}
&nbsp; &nbsp; &nbsp; 本文引用了大量白色底的图片，您可以在右下角将网站切换为暗色模式，以获得更好的浏览体验。
{% endnote %}  

{% note success modern %}
&nbsp; &nbsp; &nbsp; Hexohub现已上架[Microsoft Store应用商店](https://apps.microsoft.com/detail/9P88NBSCPFM6)，您可以选择从该渠道安装Hexohub，以获得更好的更新体验。该版本与Github发布的版本相互独立，互不影响。请注意，**由于微软的审核机制，微软商店中的版本通常会落后于Github发行版若干版本，如果您追求稳定，可以选择商店中的版本，如果您想即时体验新功能，请到Github使用最新发行版**。   
{% btn 'https://apps.microsoft.com/detail/9P88NBSCPFM6',微软商店,far fab fa-windows,blue larger %}{% btn 'https://github.com/forever218/HexoHub/releases',Github发行版,far fab fa-github,red larger %}
{% endnote %}
    
&nbsp; &nbsp; &nbsp; 从今年6月份开始，微软取消了商店个人开发者的认证费用，可以对`msix`和`pwa`格式的发行文件进行免费的签名。在[@Soulxyz](https://soulxyz.top)的建议下，花了将近一个月的时间，将Hexohub发布在了微软应用商店，下面我会详细记录发布过程。（没错，当鸽子的这一段时间干这个去了，一边和生活对线，一边和微软对线）

{% asset_img e4eebb74baa4de633fe739bfd1cc377d.png "图片描述" %}
   
# 申请账号
&nbsp; &nbsp; &nbsp; 首先到[微软商店开发平台](https://storedeveloper.microsoft.com/zh-Hans/home)注册账号。在该页面点击“开始使用”：      

{% asset_img 92b6eefbf542f2b4e498d8d01a7d450e.png "图片描述" %}

随后进入以下界面，选择“个人开发者”，然后根据提示完成注册流程：      
   
{% asset_img 28220ba5174a85845d9947bf16ee8bd6.png "图片描述" %}
   
过一阵子，一般是几天，就能收到微软发的邮件，如果收到类似下面的邮件，说明申请开发者账号成功：   

{% asset_img d99ecd7dd4b0a86b427de4ec9657b421.png "图片描述" %}

# 填写表单
&nbsp; &nbsp; &nbsp; 完成注册后，回到[微软商店开发平台](https://storedeveloper.microsoft.com/zh-Hans/home)，点击“加入Store FastTrack计划”：   
   
{% asset_img 79bdbf53871e037e2c0b36e4dae29e95.png "图片描述" %}

随后根据自身情况，填写表单：   
   
{% asset_img 4498a592fd7f79f5fec8d0d4c1353960.png "图片描述" %}
   
# 创建应用
&nbsp; &nbsp; &nbsp; 完成上述步骤之后，打开[微软合作伙伴中心](https://partner.microsoft.com/zh-cn/dashboard/home)，选择“应用和游戏”：   
   
{% asset_img 89d4678fe4f628118bda1ff989280923.png "图片描述" %}

进入以下界面，点击“+”号，选择“PWA或MSIX应用”，即可开始发布应用程序：   
   
{% asset_img fa4f4069dad4bcf4f901d679419382b2.png "图片描述" %}
   
{% asset_img 8fb7745926dd386e207161fbf843cf6e.png "图片描述" %}
   
在随后出现的窗口中，根据应用情况如实填写。   
点击应用程序名称，即可对程序情况进行查看/修改：   
   
{% asset_img 025b84cbfaa89d761f04d1dc56ccfd89.png "图片描述" %}

# 程序包
&nbsp; &nbsp; &nbsp;  如下图所示，红色框内的信息是将应用发布到应用市场的必须项，在完成了如**定价和可用性、属性、未更改、年龄分级**等信息后，到了最关键的一步：上传msix可执行文件   
   
{% asset_img cc837880bb7bd75308b3994158117dc3.png "图片描述" %}

&nbsp; &nbsp; &nbsp;  在此之前，请确保将自己的应用程序封装为**MSI**格式或是**PWA**格式，对于不同的软件架构，构建方式有所不同，比如我的是tauri框架的软件。那么封装命令和配置大致如下：
```cmd
npm run tauri:build
```
对于配置文件：
```json
{
  "$schema": "../node_modules/@tauri-apps/cli/config.schema.json",
  "productName": "HexoHub",
  "version": "3.2.0",
  "identifier": "com.hexo.desktop",
  "build": {
    "frontendDist": "../out",
    "devUrl": "http://localhost:3000",
    "beforeDevCommand": "npm run dev",
    "beforeBuildCommand": "npm run build"
  },
  "app": {
    "windows": [
      {
        "label": "main",
        "title": "HexoHub",
        "width": 1200,
        "height": 800,
        "minWidth": 800,
        "minHeight": 600,
        "resizable": true,
        "fullscreen": false,
        "decorations": false,
        "transparent": false,
        "dragDropEnabled": true,
        "visible": false
      }
    ],
    "security": {
      "csp": "default-src 'self' ipc: http://ipc.localhost http://localhost:* ws://localhost:* http://* https://*; connect-src 'self' ipc: http://ipc.localhost http://localhost:* ws://localhost:* ws://* wss://* http://* https://*; script-src 'self' 'unsafe-inline' 'unsafe-eval' http://localhost:*; style-src 'self' 'unsafe-inline' http://localhost:* http://* https://*; img-src 'self' asset: http://asset.localhost http://localhost:* data: blob: http://* https://*; font-src 'self' http://localhost:* data: http://* https://*; frame-src 'self' http://localhost:* http://127.0.0.1:* http://* https://*",
      "assetProtocol": {
        "enable": true,
        "scope": [
          "**"
        ]
      }
    }
  },
  "bundle": {
    "active": true,
    "targets": "all",
    "icon": [
      "icons/icon.icns",
      "icons/icon.ico",
      "icons/icon.png"
    ],
    "windows": {
      "nsis": {
        "installerIcon": "icons/icon.ico",
        "installMode": "currentUser",
        "languages": [
          "SimpChinese",
          "English"
        ],
        "displayLanguageSelector": true,
        "compression": "lzma",
        "startMenuFolder": "HexoHub",
        "installerHooks": "../public/installer.nsh"
      },
      "wix": {
        "language": [
          "zh-CN",
          "en-US"
        ]
      }
    }
  }
}
```
&nbsp; &nbsp; &nbsp;  更多关于tauri的封装细节，参考{% btn 'https://tauri.app/zh-cn/distribute/',官方文档,far fa-file-excel,green larger %}

## 格式转换
&nbsp; &nbsp; &nbsp;  下载官方的[MSIX Packaging Tool](https://learn.microsoft.com/en-us/windows/MSIX/packaging-tool/tool-overview)，此软件将用于把**MSI**转换为**MSIX**：   
   
{% asset_img fe18b3cd94eb47d28075260f6b03e053.png "图片描述" %}  
   
&nbsp; &nbsp; &nbsp;  安装选项全部选择默认即可，他会自动检查电脑上的构建路径，如果有问题显示安装失败，检查网络连接（使用魔法）然后重启电脑一般就能解决。进来之后选择第一个“创建你的应用包”：   
   
{% asset_img 8df4212df7d6a826530798018502e706.png "图片描述" %}   
   
选择“在此计算机上创建”：   
   
{% asset_img 2f8fa735e8b810e35d47aa957dd34413.png "图片描述" %}
   
确认驱动为**已安装**状态，点击下一步：   
   
{% asset_img d4b58abfbed1193ed011d7388a972f58.png "图片描述" %}

随后点击浏览，找到之前封装好的**MSI**文件，选择**不对程序进行签名**：   
   
{% asset_img 25fb39c1b195a06276527f0c94a39586.png "图片描述" %}

随后在“程序包信息”内，填入信息，相关的信息在上述的[微软合作伙伴中心](https://partner.microsoft.com/zh-cn/dashboard/home)中：
   
{% asset_img 9b1edd467ed575de7fddf8b38e1777a1.png "图片描述" %}
   
{% asset_img 8cd9d3675c14463a3cde2325543f2b99.png "图片描述" %}

&nbsp; &nbsp; &nbsp;  接下来的步骤默认即可，等待程序转换完成，得到一个**MSIX**文件，返回[微软合作伙伴中心](https://partner.microsoft.com/zh-cn/dashboard/home)界面。将这个文件上传到“程序包”：   
   
{% asset_img ad9d135c6d68a75de31d1fbcc7e550c6.png "图片描述" %}

&nbsp; &nbsp; &nbsp;  微软会立即对该程序包进行安全性检查，对于一些可疑的、未声明的、不安全的权限/功能，微软会要求你提供相关详细的描述（也就是你的程序为什么要使用这个权限/功能）。比方说，如果你也是用tauri框架构建的软件，然后使用工具转换为了MSIX，那么在上传程序包之后，大概率会遇到这个***runFulTrust***的警告，去网上搜一下这个权限是什么（当然了，问AI最方便），将其填入该权限描述即可。   
  
{% asset_img 8a88d39d0250501692b3ce65c7e05b17.png "图片描述" %}

# 语言问题
&nbsp; &nbsp; &nbsp;  在合作伙伴界面，可以查看当前应用支持的语言，但是微软在这里设计的相当狗屎{% inlineimage 644f753821e5ff2f95e60d5184bd4cb4.png, height=90px %}，居然只能从程序包里检测，不能手动修改，所以如果在构建阶段没有配置好的话，那么很大概率微软只会给你一个默认的“支持英文”，即使你的程序里有多种语言支持。   
   
&nbsp; &nbsp; &nbsp;  在这一步折腾了相当久，最后还是[@Soulxyz](https://soulxyz.top)找到了解决方法。首先打开之前安装的微软程序包工具，选择**程序包编辑器**：   
   
{% asset_img 888ec6570c56ee61cb62764c8e910ef4.png "图片描述" %}  
   
打开有语言问题的**MSIX**文件，然后下拉，找到***清单文件***。点击打开文件：  
   
{% asset_img 2c6374f0aef80bda163fcb6d620027e2.png "图片描述" %}

随后会自动打开一个文本文件，找到这行：  
   
{% asset_img f4f6a1c0f915d7de86402988dd97900b.png "图片描述" %}

默认应该只有一个`Resource Language="en-us"`，我们仿照这个格式，往下列出我们程序支持的语言，比如中文。格式应该为：
```xml
<Resource Language="en-us"/>
<Resource Language="zh-cn"/>
<Resource Language="............"/>
......
```
&nbsp; &nbsp; &nbsp;  随后保存文件，回到微软程序包工具，点击**保存**。再将保存之后的**MSIX**文件上传到合作伙伴中心，就能识别到支持的语言了。

{% asset_img 6d6ae1693120d96beac7c61a3ccbac5f.png "图片描述" %}

# 发布应用
&nbsp; &nbsp; &nbsp;  一切准备就绪后，在合作伙伴中心界面点击提交认证，即可开始发布应用：   
   
{% asset_img 2981614030cab9fcecab1901e3a7c286.png "图片描述" %}

提交后，会陆续看到以下界面，一般是3-5个工作日：   
   
{% asset_img 480de19971888b8c2d1f127f6e3d808d.jpg "图片描述" %}
{% asset_img 7ffc0186eb3ff8fcc0600a817758a2d8.jpg "图片描述" %}
{% asset_img b7f482f9e516f557f3a271cdd4a43823.jpg "图片描述" %}

至此，我们就将自己的应用程序发布在Microdoft Store上了。

{% asset_img e4eebb74baa4de633fe739bfd1cc377d.png "图片描述" %}

# 后记
## 关于Hexohub
&nbsp; &nbsp; &nbsp;  上架之后，身边有朋友问我，为什么不是付费的软件？我认为，很多事情只要和金钱扯上关系，那就会失去其原本的“味道”。Hexohub的初是：**一个免费、开源的程序，旨在提供更好的博客体验**，所以我上架微软商店的目的很简单，拓宽用户渠道，为更多有需要的人提供便利。    
   
&nbsp; &nbsp; &nbsp;  另一方面，在我之前学习过程中，从各种开源社区中受益匪浅，完全是无条件、无成本的获取知识，现在，是时候为开源做出自己的贡献了，***开源万岁！***
   

&nbsp; &nbsp; &nbsp;  本项目使用的是[MIT](https://choosealicense.com/licenses/mit/)开源协议，这意味着任何人都可以随心所欲地使用遵循该协议的代码（包括但不限于免费使用、复制、修改、合并、出版发行、再授权），唯一的条件是保留原作者的***署名***，所以你可以使用开源代码，定制自己喜欢的功能，然后发布它，收获自己的用户群体，COOL!。  
   
&nbsp; &nbsp; &nbsp;  如果你有好的想法，欢迎提交自己设计的版本，我将很乐意为你创建一个独立的分支！

## 关于微软商店
&nbsp; &nbsp; &nbsp;  说实话，我觉得微软商店真的是相当失败的产品，一手好牌打的稀烂：***一个拥有近乎垄断地位的操作系统，其自带的应用商店却未能取得同等的成功***，我们可以观察一下，现在电脑上的应用有多少是来自Microsoft Store？这是一个很深刻的问题，有非常多的原因导致了这个现象，引用DEEPSEEK的总结：

> 微软商店的“失败”可以归结为：它试图在一个已经拥有极度自由、成熟和去中心化生态的系统中，建立一个中心化的“围墙花园”。 对于大多数Windows用户和开发者而言，他们已经习惯了这种自由，并没有强烈的动力去改变习惯，转而使用一个在早期阶段体验不佳、限制颇多的官方商店。  

   &nbsp; &nbsp; &nbsp;  另外我还要吐槽一下微软商店的推流机制，无论什么时候打开，主界面永远是腾讯、OFFICE这些东西，看着就心烦：
   
{% asset_img e328d3f20e111d1b8215b972cb27aa3e.png "图片描述" %}

&nbsp; &nbsp; &nbsp;  总而言之，Microsoft Store想要达到Apple Store或者别的什么应用商店那样的地位，还有相当长的道路要走。

## 关于微软
&nbsp; &nbsp; &nbsp;  上述的发布流程，看上去是相当简单，但却实打实的花了我将近一个月的时间，很大原因是因为微软一些莫名其妙的操作，我几乎每走一步就能踩一个坑。看上去微软商店开发者平台是为开发者提供了便利，实际上却让开发者花了很多不必要的时间，去处理一些无关紧要的东西。   
   
&nbsp; &nbsp; &nbsp; 举一个例子，我想登录一个之前的微软开发者账号，却陷入了一个双重验证的死循环：***“我们已向您的手机Authentictor验证器发送了一个代码。输入该代码以使您登录”***，但是很幽默的是，手机上登录Authentictor验证器***同样需要该代码***。不是哥们？{% inlineimage 6737eaff5b2455ce44eb68b44647cfec.png, height=90px %}这就好比你忘记了邮箱的登录密码，然后你点击忘记密码，却提示修改密码的邮件已发送到你的邮箱。   
   
&nbsp; &nbsp; &nbsp; 一开始我以为是我的问题，直到去国外的讨论社区Reddit，铺天盖地的这方面的吐槽：
{% asset_img 15eb3f7e90b05e502eb721c284966004.jpg "图片描述" %}
{% asset_img ed640a78a082f4d20c4d8b2b53294e64.jpg "图片描述" %}
{% asset_img 36a990d1ac4baeb260ef0ca108e66294.jpg "图片描述" %}
{% asset_img d134639619da91f9d8ee4e4b4f59e76c.jpg "图片描述" %}   
   
哈哈哈哈，释怀的气笑了，微软，看看你在干什么！！！

## 附录
&nbsp; &nbsp; &nbsp;   附上几段我和另一个开发者的讨论：   
   
{% asset_img 6c5003b2c963eadcc510529f18f7a647.jpg "图片描述" %}

# 参考
&nbsp; &nbsp; &nbsp;本文参考了以下网页/文章：   
   
https://gezihuzi.github.io/2024/04/05/publish-tauri-app-to-ms-store.html   

https://post.smzdm.com/p/ado6n98x/   
   
https://aka.ms/submitwindowsapp   
   
https://learn.microsoft.com/zh-cn/windows/apps/publish/partner-center/open-a-developer-account?tabs=individual   
   
https://tauri.app/zh-cn/distribute/



