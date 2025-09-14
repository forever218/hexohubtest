---
title: Minecraft食用指南
date: 2024-04-04 11:30:33
tags: 
- 游戏
- minecraft
cover: 6.jpg
background: url(6.jpg)
publish_location: 山西省-太原市-尖草坪区
---
{% note blue 'fas fa-bullhorn' modern %}
`注意：`本文提到的是微软的“Minecraft”，而不是网易的“我的世界”。这两者有着本质上的区别。
{% endnote %}
# 基础

## 正版和盗版
正版的Minecraft是微软下面的一款买断制游戏，你可以在微软的应用商店、[Minecraft官网](https://www.minecraft.net/zh-hans)上购买，基岩版和java版的捆绑包售价89元（这貌似是最便宜的了）.
{% hideToggle 点击展开官网购买教程（已购买或打算玩盗版请忽略） %}
进入上述[官网](https://www.minecraft.net/zh-hans),将会看到如下界面：
{% asset_img 0.jpg 官网界面 %}
不要选择`前往网易`，点击`继续浏览该网站`。
然后右上角`登录`，用自己的微软账号登录就行，
{% asset_img 1.jpg 登录 %}
这里也不要选择前往网易，点下面的小字`STAY ON MINECRAFT.NET`
{% asset_img 2.jpg 微软账号登录 %}
登录完之后，点右上角的按钮`获取minecrat`：
{% asset_img 3.jpg 获取minecraft %}
然后这里就看个人，119和89都可以，两个基础版本都包含，119的多了一些皮肤之类的东西而已。
{% asset_img 4.jpg 获取minecraft %}
最后下单，结账即可。
`如何知道自己是否购买成功？`在右上角有你的微软账号名称，下拉有`我的档案`一栏，进去就能看见自己已经购买的游戏。
{% endhideToggle %}
有正版账号和盗版之间最大的区别有两点：
1. 一些（极少数官服）服务器会有正版验证，只有购买了正版的账号才能进入
2. 盗版账号在联机时（似乎）只能使用默认的皮肤

除此之外，盗版和正版没有一点区别，游戏体验完全一致。

## 基岩版（Bedrock Edition）和JAVA版（java Edition）
Minecraft两个不同的版本，Java版可在Windows、Mac以及Linux上运行；基岩版仅能在Windows上运行。另外，手机上的minecraft是基岩版的移植版本。
除了游戏内特性的些许不同，两者最大的区别在于，java版的生态更加开放和自由，玩家可以很轻松的加入各种mod、插件、光影、材质等等，而想要在基岩版中加入这些，玩家需要付出几倍的努力。
开一个java版的服务器非常简单，而基岩版非常困难。一个很好的比喻是：java版是安卓系统，基岩版就是苹果系统。一个是给你瞎折腾的，一个是最纯洁的原版。
以下是官方给出的对比：
|  | java版 | 基岩版 |
| :----:| :----: | :----: |
| 跨平台兼容性 | Windows、Mac 和 Linux 跨平台联机游玩 | 在 Windows 10、Windows 11、Xbox、Nintendo Switch、PS4 和移动平台之间进行跨平台联机游戏 |
| 分屏多人游戏 | X | O |
| 游戏手柄/触控支持 | X | O |
| Minecraft 商城 | X | O |
| 可下载内容 (DLC) | X | O |
| 模组 | O | X |
| 官方版本多人联机游戏服务器 | X | O |
| Realms | O | O |
| Realms Plus | X | O |
| 搭建您的专属服务器 | O | O |
| 成就/奖励 | X | O |
| 局域网或 WiFi 多人游戏 | O | O |
| Microsoft 帐户的家长控制功能 | X | O |
| 光线追踪 | X | O |

就这么一看，基岩版功能似乎比java版要多不少，然而就开放性这点而言，java版能凭这点完全打败基岩版，基岩版里能做到的，Java版一定也能，但Java版能做到的，基岩版可就不一定了。
举个简单的例子：在上述官方的对比表中，基岩版支持光线追踪，java版不行，但是在java版加入了诸如optifine等三方高清修复mod之后，玩家可以很方便的使用各种各样的光影，一些效果甚至远超基岩版的光线追踪。

## 下载启动器
无论是否购买了正版，这一步我们正式开始安装minecraft(java版)。最简单的方式是通过启动器安装。
官方启动器在各个方面都不如第三方启动器，所以这里我们选择用PCL2启动器来安装。（官方启动器可以在官网或是微软应用商店里下载到）。
{% btn 'https://afdian.net/p/0164034c016c11ebafcb52540025c377?eqid=d3f406ae0020b8b400000006652b4708',点击前往PCL2原作者页面下载启动器,far fa-hand-point-right,blue larger %}
下载后得到一个`.exe`文件，强烈建议在`非C盘`的地方新建一个文件夹，再把那个exe文件放进去，这样可以省去很多麻烦。
完成之后打开PCL2启动器：
{% asset_img 5.jpg PCL2 %}
点击上面的`下载`，选择mc版本，按照指引安装java版本文件、下载Minecraft文件安装即可。
需要注意的是，如果购买了正版，请在`启动`界面登录你的微软账号，否则选择`离线`再启动。
{% note success mordern %}
Minecraft，启动！！！
{% endnote %}

# 进阶

## 模组（MOD）
{% note info modern %}
事实上，一开始的java版minecraft，mojang并没有说明允许玩家使用第三方mod，现在的mod加载器实际上是对游戏的反编译过程。后来mojang对这样的行为一直保持睁一只眼闭一只眼的态度，也就默许了玩家的二次开发。
{% endnote %}
在java版的minrcraft中，玩家需要通过加载器来加载mod，目前主流的有两个加载器，一个是[fabric](https://fabricmc.net/)，一个是[forge](https://files.minecraftforge.net/net/minecraftforge/forge/)。虽然都是minecraft的mod加载器，但由于他们有着完全不同的代码架构，所以他们之间mod并不能兼容（fabric的mod不能运行在forge上，反之亦然）。这也是为什么很多mod你能看见fabric和forge两个版本的原因，mod作者需要兼顾两个加载器。
{% hideToggle 点击展开图片 %}
{% asset_img 7.jpg fabric %}
{% asset_img 8.jpg forge %}
{% endhideToggle %}
forge是老牌的加载器，诞生时间比fabric早得多。forge的优点：诞生时间早，生态完善，支持非常多mod，并且兼容性良好，允许使用更多的插件；forge唯一缺点就是，比起fabric体积更大更笨重，需要的内存和算力更多。fabric是后起之秀，还处于青年期，很多乱七八糟的bug和问题，生态也一般般，很多mod都没有。但好处就是它非常轻量化，加载和启动迅速。
现在来说说如何安装加载器和模组。以PCL2启动器为例：在`下载`-`自动安装`中，你可以在安装游戏版本的时候选择forge或fabric的版本（一般都是选择最新的），点击对应的版本即可和游戏一起安装。请注意，forge和fabric只能选择其一进行安装。
{% asset_img 9.jpg 安装加载器 %}
安装完成之后，就可以点击`下载`-`mod`，安装各种模组了。
{% note warning modern %}
一些mod之间可能存在冲突，安装过多的mod需要谨慎，否则启动器可能不会正常启动游戏。
{% endnote %}
## 光影（Shader）
光影在不改变游戏原汁原味的情况下，极大的改善了minecraft的游戏体验.
{% asset_img 10.jpg 无光影 %}
{% asset_img 11.jpg 有光影 %}
{% asset_img 12.jpg 无光影 %}
{% asset_img 13.jpg 有光影 %}
有了第三方启动器，玩家可以很方便通过光影mod来使用光影，目前常见的光影mod是[optifine](https://www.optifine.net/home)。以PCL2为例，还是在`下载`-`自动安装`里，选择对应的optifine版本安装即可。
{% note info modern %}
optifine经常被归类为`mod`，但事实上它并不依赖于模组加载器工作，你在没有安装fabric或forge的情况下依旧能使用它。所以我认为叫它`光影加载器`更加合适一点。
{% endnote %}
{% note warning modern %}
optifine与fabric加载器冲突，你不能同时安装他们两个。有个替代方案是使用optifabric，，使你既可以使用fabric来加载mod，也可以使用光影。你依旧可以在PCL2的`下载`-`自动安装`找到optifabric.
{% endnote %}
## 材质
未完待续...
## 本地（局域网）联机
...
## 远程联机
...
## 服务器(server)
...

