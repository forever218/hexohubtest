---
title: 博客搭建日记--B站篇
date: 2021-06-11 10:54:14
tags: note
cover: 1.jpg
background: url(1.jpg)
---
  忽然想起movies分类页里啥也没有，只有两个onedrive的视频分享链接，可以说是空空如也，于是决定再加一点内容。在[hexo](hexo.io)的官网找了一下，发现一的个很不错的插件，支持将B站的视频嵌入到博客中。
{% note info modern %}
为什么不用Dplayer？诚然，[Dplayer](https://github.com/MoePlayer/DPlayer)作为一个网站嵌入式视频播放器相当优秀，功能强大实用.调用也很方便。但是不足之处在于不支持类似于B站的流媒体格式，要求你必须拥有.mp4等格式的原始文件，这就极大的限制了它的使用范围。再说，哪个白嫖党会去网上租空间放视频？=。=
{% endnote %}
  该项目名叫[hexo-tag-bilibili](https://github.com/Z4Tech/hexo-tag-bilibili),安装命令如下：
```bash
cnpm install --save hexo-tag-bilibili
```
但是在官网上，安装的命令是这样子的：
```bash
npm install --save hexo-tag-bilibili
```
如果执行官网的这个命令，就会出现：
{% asset_img 0.png 实例图片 %}
{% note danger %}
请注意当前的版本是否符合其要求
{% endnote %}
可以看出，报了一堆的错，没有安装成功。折腾了好久，翻回之前的博客文章，才发现`npm`y应该换成`cnpm`，原因参见前几篇的文章。
好不容易安装完，又有新的问题，真是吐了。弄完之后随便塞个B站视频的id进去发现播放不了，研究了一下发现插件生成的网址key居然用的是av？！我的天，众所周知B站现在全是bv号，只有仅存的几个还保留着av号。
{% asset_img 2.png 逝去的av %}
{% asset_img 3.png 现在的bv %}
唉，实在没办法，这个插件版本太旧了，项目作者也不知道多久没有更新过了，将就着用吧。
