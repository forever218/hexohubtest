---
title: Hexohub开发日志4
date: 2025-10-07 16:21:02
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

{% note success modern %}
强烈建议您升级到[v3.0.0](https://github.com/forever218/HexoHub/releases/tag/v3.0.0)版本。在3.0.0版本中，应用体积、功能性均有较大程度的优化。
{% endnote %}



&nbsp; &nbsp; &nbsp; 截至撰文，[HexoHub](https://github.com/forever218/HexoHub)已更新到[v3.0.0](https://github.com/forever218/HexoHub/releases/tag/v3.0.0)。3.0.0版本主要有以下**重大更改**： 

 - 引入Tauri框架，借助部分系统自带的webview，大幅缩小应用体积   
 - 优化electron框架下的打包体积，删除不必要的语言包
 - 为项目引入自动构建、发布流程，借助github action实现自动工作流  
 - 加入图片直接复制进资源文件夹的功能
 - 支持更多ai模型接入

{% note primary modern %}
在这里非常感谢[开哥](https://blog.soulxyz.top/)，为这个项目做出了巨大贡献。  

{% asset_img 36c994508698952dd8f301d070462751.png "图片描述" %}

有关此版本的更多内容，可以参见{% btn 'https://blog.soulxyz.top/2025/10/07/HexoHub.html',🎉开哥的博客,far fa-hand-point-right,blue larger %}
{% endnote %}
 
# 体积
&nbsp; &nbsp; &nbsp; v3.0.0版本对electron体积进行了大幅度优化：

| - | setup.exe | 安装所需空间 |
|-----|-----|-----|
| v2.6.0 | 85MB | 294MB |
| v3.0.0 | **77MB** | **250MB** |

&nbsp; &nbsp; &nbsp; 如果您使用Tauri版本，则setup.exe仅为**5MB**，安装体积仅为**20MB**


# 基础使用 
&nbsp; &nbsp; &nbsp; 在最新的3.0.0版本中，分为**Tauri**和**Electron**两个版本，下文将说明您该如何选择使用。  
> &nbsp; &nbsp; &nbsp; **省流**：Tauri适合较新的系统，体积很小；Electron适合较旧的系统，体积稍大但稳定。
## Tauri
&nbsp; &nbsp; &nbsp; Tauri的基本思路是：使用系统内置的Webview框架，不重复造工具。在**Windows 10 2004（内部版本19041）**及以上版本中，系统已经内置Webview2，您只需要下载5MB的安装程序（Hexohub-tauri-setup），在硬盘中预留20MB的安装空间即可：  

{% asset_img 15deed193fd3a31f6869ea6950504808.png "图片描述" %}

&nbsp; &nbsp; &nbsp; 如果您使用的是更早期的win版本，依旧可以使用Tauri框架的Hexohub，会在安装的时候自动为您下载webview。但是鉴于下载速度及空间占用（官方的下载源很慢，并且安装完体积和Electron差不多），旧版本的系统还是建议您使用Electron版本。

## Electron
&nbsp; &nbsp; &nbsp; 考虑到这是第一个使用Tauri框架的版本，许多系统兼容性尚未得到验证（尤其是Linux），依然保留了传统的Electron应用，如果您的系统因为某些原因没有webview（并且您也没有安装webview的意愿），或者说webview无法正常使用，您可以选择Electron版本（Hexohub-electron-setup）。
&nbsp; &nbsp; &nbsp; Electron内置了一个浏览器内核，对外界环境要求不那么高，在一些情况下会更加稳定。  

## Mac/Linux
&nbsp; &nbsp; &nbsp; 在此次更新中，首次构建了mac和linux系统下的发行版，但是由于缺乏完善的测试环境，该发行版的功能性、可用性尚未得到验证，如果您在使用中遇到任何问题，欢迎留言或者到github提交[issues](https://github.com/forever218/HexoHub/issues)，我会尽快处理。

# 图片复制
&nbsp; &nbsp; &nbsp; 在2.6.0及更早的版本中，您需要将图片提前放入资源文件夹，再将图片拖入Hexohub，图片才能被正确引用并渲染。而在此次更新中，您可以将任意位置的图片（例如桌面）直接拖入编辑框内，Hexohub会自动复制该图片到资源文件夹下，并填入`{%asset_img%}`标签，无需您再动手操作文件。


{% asset_img 8f22baf987b2baa74a59f93ce5d4aebc.png "图片描述" %}


# 其他更新
&nbsp; &nbsp; &nbsp; [开哥](https://blog.soulxyz.top/2025/10/07/HexoHub.html)那边写过了，这里就不再赘述，省流截图版：

{% asset_img 998423fd0958d1c18f2323b4f4810a19.png "图片描述" %}

# 合作
&nbsp; &nbsp; &nbsp; [开哥](https://blog.soulxyz.top/2025/10/07/HexoHub.html)加入项目之后，进度明显快了许多。不愧是资深~~二次元~~技术宅，在很多方面都有比我敏锐得多的察觉力，tauri框架、图片复制、自动构建流程等都是他提出并实现的，上哪再去找这么强~~有意思~~的partner啊。

{% asset_img 8b108dd74a54c6abf502dad9173c13d9.png "图片描述" %}

{% asset_img 962cd223ff4f936e4bfbe4a957950102.png "图片描述" %}

{% asset_img a96638f28a22f9fe391d98c22c63ca19.png "图片描述" %}

{% asset_img 585003fbbc435899d08bcb3040dc58c7.png "图片描述" %}

那还说啥了兄弟，等我回广州请你干饭不就完了@[开哥](https://blog.soulxyz.top) {% inlineimage 2.png, height=70px %}