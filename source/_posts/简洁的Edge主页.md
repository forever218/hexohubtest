---
title: 简洁的Edge主页
date: 2022-12-05 19:31:33
tags: html
cover: 0.jpg
---
  用手机浏览器VIA用久了，居然觉得Edge没那么简洁了。现在每次打开Edge，看着乱七八糟的收藏夹和各种选项就烦，于是决定调用自己那少得可怜、不靠谱的js和html知识，写一个简洁的初始页（复杂了也写不出来）
  首先我想要实现的是一个打字功能（和博客的主页挺像），但考虑到浏览器可能在断网的情况下启动，就不用调用网络接口了（一言等），内容全部储存在代码容器中。
  先在任意位置创建一个`.html`文件，第一次写的html如下：
```html
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
  <title>Welcom,然！</title>
  <style>
    .divcss5{word-wrap:break-word;font-size: 38px;}
  </style>
</head>
<body>
<div class="divcss5"  id="aa">
   
</div>
<div style="display:none" id="w">
  今日事，今日毕
</div>
<script language="javascript">
  var index=0;
  var word=document.getElementById("w").innerHTML;
  function type(){
    document.getElementById("aa").innerText = word.substring(0,index++);
  }
  setInterval(type, 300);
</script>
</body>
</html>
```
保存后打开Edge，按以下步骤设置：
{% asset_img 1.gif 步骤%}
其中，将上面写的`.html`文件地址填进去，保持即可。
第一次弄完就发现自己傻逼了，打出来的字不居中，缩在左上角，难看的要死，于是在`style`标签中，加入居中的元素，
```html
<style>
  .divcss5{word-wrap:break-word;font-size: 38px;text-align: center;line-height: 250px}
  </style>
```
之所以`line-height`没有用center值，是因为发现center对`line-height`似乎不起作用，一定要是某个数，所以根据自己的喜好改成了250px。
最终的代码是：
```html
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
  <title>Welcom,然！</title>
  <style>
    .divcss5{word-wrap:break-word;font-size: 38px;}
  </style>
</head>
<body>
<div class="divcss5"  id="aa">
   
</div>
<div style="display:none" id="w">
  今日事，今日毕
</div>
<script language="javascript">
  var index=0;
  var word=document.getElementById("w").innerHTML;
  function type(){
    document.getElementById("aa").innerText = word.substring(0,index++);
  }
  setInterval(type, 300);
</script>
</body>
</html>
```

实现的效果如下：
{% asset_img 2.gif 效果%}
其中以下可根据个人喜好自定义：
- `<title>Welcom,然！</title>`处内容为页面主标题
- `<div style="display:none" id="w">今日事，今日毕</div>`处为打字内容
- `setInterval(type, 300)`为打字的速度，可自行更改
- `font-size: 38px`处为字体大小

但是简洁的主页有什么用？其实没什么用，只能说，看着好看而已。另外，电脑上花里胡哨的东西又增加了。



  