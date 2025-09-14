---
title: 修复两个小bug
date: 2023-11-11 18:15:00
tags:
- Hexo
- butterfly
- 博客
- 前端
cover: 0.jpg
background: url(0.jpg)
publish_location: 山西-太原
---
# 修复BUG
昨天博客版本又进行了一次迭代，主要修复了以下两个BUG：
## 小风车
之前对H1~H6行标前的转动小风车的样式进行过一次调整，使之在PC端上看起来更大，适应浏览器的主体布局，可是忽略了对手机端的适配，让其小风车在手机端显示的时候会超出文章卡片，看起来很不协调，所以将部分css改回原来的版本，适配手机端
```css
/* 文章页H1-H6图标样式效果 */
/* 控制风车转动速度 4s那里可以自己调节快慢 */
h1::before,
h2::before,
h3::before,
h4::before,
h5::before,
h6::before {
    -webkit-animation: ccc 4s linear infinite;
    animation: ccc 4s linear infinite;
}
/* 控制风车转动方向 -1turn 为逆时针转动，1turn 为顺时针转动，相同数字部分记得统一修改 */
@-webkit-keyframes ccc {
    0% {
        -webkit-transform: rotate(0deg);
        transform: rotate(0deg);
    }

    to {
        -webkit-transform: rotate(-1turn);
        transform: rotate(-1turn);
    }
}

@keyframes ccc {
    0% {
        -webkit-transform: rotate(0deg);
        transform: rotate(0deg);
    }

    to {
        -webkit-transform: rotate(-1turn);
        transform: rotate(-1turn);
    }
}
/* 设置风车颜色 */
#content-inner.layout h1::before {
    color: #ef50a8;
    margin-left: -1.55rem;
    font-size: 1.3rem;
    margin-top: -0.23rem;
}

#content-inner.layout h2::before {
    color: #fb7061;
    margin-left: -1.35rem;
    font-size: 1.1rem;
    margin-top: -0.12rem;
}

#content-inner.layout h3::before {
    color: #ffbf00;
    margin-left: -1.22rem;
    font-size: 0.95rem;
    margin-top: -0.09rem;
}

#content-inner.layout h4::before {
    color: #a9e000;
    margin-left: -1.05rem;
    font-size: 0.8rem;
    margin-top: -0.09rem;
}

#content-inner.layout h5::before {
    color: #57c850;
    margin-left: -0.9rem;
    font-size: 0.7rem;
    margin-top: 0rem;
}

#content-inner.layout h6::before {
    color: #5ec1e0;
    margin-left: -0.9rem;
    font-size: 0.66rem;
    margin-top: 0rem;
}
/* s设置风车hover动效 6s那里可以自己调节快慢*/
#content-inner.layout h1:hover,
#content-inner.layout h2:hover,
#content-inner.layout h3:hover,
#content-inner.layout h4:hover,
#content-inner.layout h5:hover,
#content-inner.layout h6:hover {
    color: var(--theme-color);
}

    #content-inner.layout h1:hover::before,
    #content-inner.layout h2:hover::before,
    #content-inner.layout h3:hover::before,
    #content-inner.layout h4:hover::before,
    #content-inner.layout h5:hover::before,
    #content-inner.layout h6:hover::before {
        color: var(--theme-color);
        -webkit-animation: ccc 6s linear infinite;
        animation: ccc 6s linear infinite;
    }
```
## 卡顿
最近忽然发现，自己的博客在互动的时候特别卡顿，各种动画都会掉帧，卡的不行。奇怪的是只有在电脑上，当且仅当浏览器画幅放大到一定比例的时候，卡顿才会出现，手机则一点问题都没有。
### 问题排查
首先想到的是兼容性问题，有可能是某些css或js和当前浏览器有冲突，导致了卡顿。于是马上去升级了edge，又用chrome浏览器试了一下，还是卡顿，浏览器的原因排除。
再就是可能是网络的问题。打开浏览器的控制台，发现网站各个文件的响应都非常迅速，并且就算是在本地预览，卡顿问题依旧存在，因此网络原因也排除了。
这样一来，就只剩一种情况了：网站本身出了问题。根据经验，这样的问题一般出在css文件里，因为比起css,大部分js的作用范围都有限，不会导致这样全局卡顿的情况。
接下来我使用排除法，一点一点的删掉css里的各个标签，直到网页不掉帧为止。最后问题终于找到了：由于使用过多`backdrop-fliter`标签，使得电脑渲染压力过大从而导致的掉帧。
`backdrop-fliter`是一个css的标签，可以实现3D毛玻璃的效果，但是非常消耗性能资源，俗称`css性能杀手`，我当时写的时候并不知道，在每个页面和卡片都加了点进去，才导致了非常严重的掉帧。
### 解决
解决方案很简单，把`backdrop-fliter`删掉（注释掉）即可：
```css
/* 首页文章卡片 */
#recent-posts > .recent-post-item {
    background: var(--trans-light);
    /* backdrop-filter: var(--backdrop-filter);*/
    border-radius: 25px;
    border: var(--border-style);
}

/* 首页侧栏卡片 */
#aside-content .card-widget {
    background: var(--trans-light);
    /* backdrop-filter: var(--backdrop-filter);*/
    border-radius: 20px;
    border: var(--border-style);
}

/* 文章页、归档页、普通页面 */
div #post, div#page, div#archive {
    background: var(--trans-light);
    /* backdrop-filter: var(--backdrop-filter);*/
    border: var(--border-style);
    border-radius: 20px;
}

/* 导航栏 */
#page-header.nav-fixed #nav {
    background: rgba(255, 255, 255, 0.7);
    /* backdrop-filter: var(--backdrop-filter);*/
}
```
当然，还有另一种方法：
https://blog.csdn.net/weixin_43561423/article/details/130691113
但是显然不如直接删掉来的痛快，并且删掉效果更加好。
# 未解决问题
当前还有个小问题，就是文章AI经常失灵，写出默认的回复，如下：
{% asset_img 1.jpg 无效回复 %}
这应该是第三方调用端口的问题，不是我的原因~
