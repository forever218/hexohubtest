---
title: 一个git的小问题
date: 2022-08-26 11:28:52
tags: [hexo,git]
cover: 1.png
---
最近在执行命令`hexo d`的时候，git老是报如下的错：
{% asset_img 0.png 报错%}
而且每次推送完成之后，都发现github page里的自定义域名没了，要再填一遍，麻烦得很。后来问了开哥，解决方案如下：
- 首先执行命令
```git bush
git config --global core.autocrlf false
```
将自动换行替换关掉（git有时会将linux的lf换行转换成windows的crl换行）
然后看看github的根目录下有没有一个cname文件，没有的话创建一个，内容就是自己的域名。