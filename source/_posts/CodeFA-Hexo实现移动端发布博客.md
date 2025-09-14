---
title: CodeFA+Hexo实现移动端发布博客
date: 2023-09-05 10:34:46
tags:
- hexo
- butterfly
cover: 0.jpg
background: url(0.jpg)
---
# 前言
静态博客和动态博客相比，最大的劣势在于有个相对“笨重”的后端。也就是说，想要更新或是发表新文章，都要在电脑上弄，台式不用说，而手提这么沉，也不可能随身带着。动态博客完全是前端的，发表、管理都在网页上，非常方便，Codefa+Hexo是一种较为简单的方法，可以让你在安卓设备上实现对hexo的完全管理，效果和在电脑上一模一样。
# 设备推荐
理论上来说，所有安卓设备都可以使用；但是考虑到使用体验，还是建议在平板上用，因为屏幕大一点，敲代码也会舒服一点。
# 正文
## 安装code fa
下载并安装`Code FA`,{% btn 'https://www.coolapk.com/apk/com.nightmare.code',可以到酷安下载,far fa-hand-point-right,pink larger %}.这一步没什么好说的，唯一需要注意的是，新版的CodeFA已经集成了`code-server`，不需要自己另行下载。
进入codefa，等待程序自动安装并初始化完成。
{% asset_img 4.jpg 启动界面 %}
完成后，屏幕可能会白一下子，然后进入初始界面：
{% asset_img 5.jpg 初始界面 %}
当然，肯定是英文的。接下来要安装简体中文语言包。点击左侧栏`拓展`，在上面搜索`chinese`，选择第二个并点击`install`。完成后退出软件，重进即可生效。
{% asset_img 3.jpg 安装中文包 %}
如果你跟我感觉一样，敲代码的时候背景太白很不舒服的话，可以在`左下角齿轮`处，更改ui颜色：
{% asset_img 2.jpg 改颜色 %}
如果想要美化代码，并在写的时候获得更好的体验，可以搜索`pretty`插件，选择下载量最大的那个安装。
## 安装hexo所需环境
{% note info modern %}
其实就是在ubtun系统下部署hexo,所以服务器部署hexo也可以参考本教程
{% endnote %}
接下来就是正式安装hexo了，首先点击左上角`三条杠`，`终端`-`新建终端`：
{% asset_img 1.jpg 新建终端 %}
在新出现的终端里，执行：
``` bash
apt update
```
等待即可
### 安装git
然后继续执行：
``` bash
apt install git
```
### 安装nodejs
{% note danger modern %}
注意，不能先单独安装npm，例如`apt install npm`，不然会报错。不知道为什么，当时我自己安装的时候就是这样🤔
{% endnote %}
nvm是一个node的管理工具，非常好用，可以很方便的安装、卸载、切换node版本。这里我们先安装nvm，通过它来安装nodejs。
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
```
{% note warning flat %}
这一步对网络要求较高，如果有条件，可以在魔法环境下进行
{% endnote %}
然后执行以下命令来安装最新版的nodejs:
```bash
nvm install --lst
```
这里顺便列出几个常用的`nvm`命令：
```bash
nvm list   //列出已安装的node版本
nvm use  xxx   //选择使用哪个版本的node
```
`更多使用方法，参见官网：https://github.com/nvm-sh/nvm`
安装完nodejs，npm也顺带安装完成了，无需另外安装。可以通过以下命令来验证安装：
```bash
node -v
npm -version
```
如输出版本号，即安装成功。
至此，hexo所需的环境全部搭建完成，接下来就是熟悉的hexo的安装了。
## 安装hexo
我之前写过类似的文章{% btn 'https://2am.top/2020/08/04/%E5%85%B3%E4%BA%8E%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E7%9A%84%E9%82%A3%E4%BA%9B%E4%BA%8B/',点击前往,far fa-hand-point-right,red larger %}，过程类似，可以参考。
找一个地方（我是在/home下），新建一个文件夹，长按（或右键，如果用鼠标的话）它，选择`在集成终端中打开`，按着hexo的步骤来即可
{% asset_img 6.jpg hexo %}
在刚刚打开的新终端里：
```bash
npm install hexo-cli -g
hexo init blog
cd blog
npm install
```
完成hexo的安装。开始愉快的blogging!!!
PS：你可以从github上把自己的项目pull下来，慢慢安装之前的插件什么的，完成博客的迁移。
# 后记
按理来说，这样`webview`编程的效率并不高，但是得益于hexo的轻量化，构建和渲染的速度还是很快，短短几秒就可以完成渲染，不比电脑慢多少，实用性还是很高的。真正的移动端hexo！
当然，codefa其实就是在ubtun系统里用webview的方式来运行vscode,所以你还可以在里面写并运行c、java、python...等等等等，只要安装了对于的包就可以。生产力max了属于是。
# 附录
{% asset_img 7.jpg hexo %}
{% asset_img 8.jpg hexo %}
{% asset_img 9.jpg hexo %}



