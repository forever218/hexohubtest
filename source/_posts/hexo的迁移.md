---
title: hexo的迁移
date: 2022-08-23 11:41:53
tags: [hexo,技术,后端]
categories: hexo
cover: 1.jpg
background: url(1.jpg)
---
# 前言
这几天买了台新的笔记本，性能比之前的老台式还要好😂，而且考虑到上大学要一直带着，长期要使用这台电脑，所以决定从老电脑上把hexo迁移过来，方便写博客和更新站点。
# 尝试
第一时间上百度搜相关的内容[链接](https://www.baidu.com/s?wd=hexo%E6%8D%A2%E7%94%B5%E8%84%91%E4%BA%86%E6%80%8E%E4%B9%88%E5%8A%9E&rsv_spt=1&rsv_iqid=0xeeefb3540009038b&issp=1&f=3&rsv_bp=1&rsv_idx=2&ie=utf-8&tn=88093251_110_hao_pg&rsv_enter=1&rsv_dl=ts_0&rsv_sug3=12&rsv_sug1=9&rsv_sug7=101&rsv_sug2=1&rsv_btype=i&prefixsug=hexo%25E6%258D%25A2&rsp=0&inputT=8180&rsv_sug4=9125)，结果又是五花八门，众说纷纭，然后试了scdn上说的方法[链接](https://blog.csdn.net/qq_34187711/article/details/88592760),有一说一，亲测没用。
然后又试了其他一些博主的方法，也都不尽人意，`git bush`的时候老是报错。自己摸索了好久。
# 方法
经过不懈努力，终于搞定了，hexo终于完整的迁移到了新电脑上，过程如下(其实很简单):
在hexo的初始文件夹，你会看到如下结构{% asset_img 0.png hexo目录 %}
然后从旧的博客文件夹里，将以下文件（夹）复制到新博客目录相应位置：
- /blogroot/config.yml
- /blogroot/source
- /blogroot/theme
{% note primary simple %}
弄完之后，就基本上搞定了，但是可能还会存在报错的情况。这就有可能是版本的问题，可以考虑版本回退使hexo适应当前nodjs版本。当然，对此最简单的方法是将所有（包括nodejs,hexo,git）都升级至最高级。
{% endnote %}
搞定之后，执行hexo的命令就可以了。
