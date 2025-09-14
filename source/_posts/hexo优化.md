---
title: hexo优化
date: 2023-04-15 11:17:54
tags: hexo
cover: 0.jpg
background: url(0.jpg)
---
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=541326593&auto=0&height=66"></iframe>

  不知不觉，距离上次写博客已经过去了快三个月。再这样懒下去，就要变成年更博客了（恼）。实际上，这期间有很多东西是值得记录的，大学生活，羽毛球，读书笔记，人生感想等等等等。只不过真的是自己懒，不想去碰博客而已。
  当然，我也思考过这样到底有什么意义，写出来的东西究竟有谁会去看？纯纯浪费时间。每次这样想，就更没有动力了。
  后来，我想起了建博客的初衷，“能在纷繁杂乱的互联网中有一块属于自己的静谧之地，记录生活，记录感想”，这才是最初的目的啊！有一个属于自己的世界，畅所欲言，获得魔改带来的成就感，偶尔有留言的惊喜，这就是博客的意义，从来都不是有多少多少观众。
  这几天花了很多时间，用来升级博客，优化访问。
  具体改动及过程如下：
# 升级主题butterfly至4.8.1
   首先将主题配置文件_config.yml剪切到桌面`(这非常重要!!!)`，然后在/themes目录下执行：
   ```bash
   git pull
   ```
   对照桌面上旧的_config.yml文件，修改新的_config.yml文件内容，即可完成主题的平滑升级。（实测新旧配置文件并不兼容）。
   butterfly的升级主要解决了一些小问题，比如一些以jsdeliver为源的图片无法访问等。
## 删除大部分不必要组件
   在任意位置执行：
   ```bash
   npm list
   ```
   npm将会列出已经安装的包，然后：
   ```bash
   npm uninstall 包名
   ```
   即可卸载该包。
   之前在博客里安装了一堆电子时钟，计时器，那年今日等等插件，当时还好，后来jsdeliver出问题之后速度就慢下来了，自己有懒得把他们的源文件本地化，就干脆删了痛快，反正实际用处也不大。
## 将jsdeliver换成unpkg
  直接在主题配置文件最下面将jsdeliver改成unpkg就好。
  这个就不用说了，极大改善了访问质量。
## 加入顶部加载条
  在目录`/butterfly/source/css`中创建名为jiazai.css的文件，内容如下：
  ```css
  .pace {
  -webkit-pointer-events: none;
  pointer-events: none;
  -webkit-user-select: none;
  -moz-user-select: none;
  user-select: none;
}

.pace-inactive {
  display: none;
}

.pace .pace-progress {
  background: #e90f92;
  position: fixed;
  z-index: 2000;
  top: 0;
  right: 100%;
  width: 100%;
  height: 2px;
}

.pace .pace-progress-inner {
  display: block;
  position: absolute;
  right: 0px;
  width: 100px;
  height: 100%;
  box-shadow: 0 0 10px #e90f92, 0 0 5px #e90f92;
  opacity: 1.0;
  -webkit-transform: rotate(3deg) translate(0px, -4px);
  -moz-transform: rotate(3deg) translate(0px, -4px);
  -ms-transform: rotate(3deg) translate(0px, -4px);
  -o-transform: rotate(3deg) translate(0px, -4px);
  transform: rotate(3deg) translate(0px, -4px);
}

.pace .pace-activity {
  display: block;
  position: fixed;
  z-index: 2000;
  top: 15px;
  right: 15px;
  width: 14px;
  height: 14px;
  border: solid 2px transparent;
  border-top-color: #e90f92;
  border-left-color: #e90f92;
  border-radius: 10px;
  -webkit-animation: pace-spinner 400ms linear infinite;
  -moz-animation: pace-spinner 400ms linear infinite;
  -ms-animation: pace-spinner 400ms linear infinite;
  -o-animation: pace-spinner 400ms linear infinite;
  animation: pace-spinner 400ms linear infinite;
}

@-webkit-keyframes pace-spinner {
  0% { -webkit-transform: rotate(0deg); transform: rotate(0deg); }
  100% { -webkit-transform: rotate(360deg); transform: rotate(360deg); }
}
@-moz-keyframes pace-spinner {
  0% { -moz-transform: rotate(0deg); transform: rotate(0deg); }
  100% { -moz-transform: rotate(360deg); transform: rotate(360deg); }
}
@-o-keyframes pace-spinner {
  0% { -o-transform: rotate(0deg); transform: rotate(0deg); }
  100% { -o-transform: rotate(360deg); transform: rotate(360deg); }
}
@-ms-keyframes pace-spinner {
  0% { -ms-transform: rotate(0deg); transform: rotate(0deg); }
  100% { -ms-transform: rotate(360deg); transform: rotate(360deg); }
}
@keyframes pace-spinner {
  0% { transform: rotate(0deg); transform: rotate(0deg); }
  100% { transform: rotate(360deg); transform: rotate(360deg); }
}
/* 在下面修改进度条外观 */
.pace .pace-progress {
  background: #1ef4fbec; /*进度条颜色*/
  height: 3px;/* 进度条厚度 */
}
.pace .pace-progress-inner {
  box-shadow: 0 0 10px #1ef4fbce, 0 0 5px #1ecffbd0; /*阴影颜色*/
}
.pace .pace-activity {
  border-top-color: #1edafbe5;	/*上边框颜色*/
  border-left-color: #1ef4fbec;	/*左边框颜色*/
}
```
然后在主题配置文件inject中引入：
```yml
<script src="//cdn.bootcss.com/pace/1.0.2/pace.min.js"></script>
```
{% note primary simple %}
以上方法来源于轻笑[Chuckle](https://www.chuckle.top/article/13d6481a.html)
{% endnote %}
## 替换了首页字体的源
 由jsdeliver改为googlefonts
## 修改底部又拍云图标样式
 由原来又大又丑的白色图标，替换成透明居中灰色图标。
## 调整页面动画
出现延迟由原来的1s调整为0.5s,动画持续时间由原来的2s调整为1s。
## 调整全局字体样式及大小
 由原来的17px调整为15px.
## 更新了评论区qq邮箱授权码
 修复原来无法收到评论回复邮件的问题


现在看看，也没改多少东西，但真的要累死我，优化真的太难搞了。
希望以后能保持热忱，继续折腾。
