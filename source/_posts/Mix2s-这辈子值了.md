---
title: Mix2s:这辈子值了
date: 2023-05-20 10:18:29
tags: 技术
cover: 0.png
background: url(0.png)
---
前不久在咸鱼上花了380买了一台二手的mix2s，一来作为备用机，二来可以整点花活。为什么要选择mix2s?因为晓龙845的可塑性太高了，另外，作为一款当年小米主打的旗舰机，性能真的很不错。
到手后第一件要捣鼓的就是刷windows了，毕竟对于windows掌机期待了很久，而且在网上也看到了很多成功的案例，早就想试试。
这过程没什么好说的，由酷安上的一位大佬提供了一个一键刷机的软件，非常方便，连适配的windows系统下载都提供了，完全就是傻瓜式的刷入。
酷安`@某贼`
比起刷入windows,解开bl锁反而需要更多的时间和精力，不过按着小米官网的步骤来，总能解决问题。
现在来说说使用的感受。首先就是适配，做的太完美了。甚至连重力和加速度感应的适配都做了，成为一个真正的windows掌机，wifi和蓝牙也是没有任何问题。
硬件上不足就是屏幕太小了，如果不外接键盘鼠标的话，用着着实难受，点个东西都费劲，更别说输入了。不过如果日常操作量不大，比如挂着当服务器，ftp，远程桌面什么的（我就是这么干的），只有触屏也是够用的。
声音外放无法使用，这是很多适配机器的毛病。不过这点可以通过连蓝牙耳机解决。
另外，电话卡的基带也被我刷坏了，手机卡无法读取，打不了电话，数据也用不了。这点虽然很不方便，但有wifi可以解决上网问题，只是作为备用机就差点意思了。（不能读手机卡的手机，嗯......）
后来又在安卓系统下以chroot容器的形式运行了一个centos系统，这样一来，一台小小的mix2s上就共存了3个不同的操作系统，真的是承受了太多本不该承受的，远超mix2s本来的价值了。
 # 系统情况
 ## windows arm
<iframe src="https://7llb7h-my.sharepoint.com/personal/lisiran_7llb7h_onmicrosoft_com/_layouts/15/embed.aspx?UniqueId=66ae3617-3d3b-46e1-bcd3-fd5f0658ded6&embed=%7B%22ust%22%3Atrue%2C%22hv%22%3A%22CopyEmbedCode%22%7D&referrer=OneUpFileViewer&referrerScenario=EmbedDialog.Create" width="640" height="360" frameborder="0" scrolling="no" allowfullscreen title="WeChat_20230618091128.mp4"></iframe>

在手机上运行的 `windows arm` 和在电脑上真正的`windows x86`是有区别的。其不同的处理器架构会导致一些软件不能正常使用，比如docker的某些版本在`arm`上就不能运行。
性能方面`arm`比`x86`差得多了，更何况在手机上运行是还存在一个指令集编译的过程，使得性能进一步降低。实测的情况是，在给`ARM`分配了完整的6g手机内存后，运行一个1.19的java版mc服务器，内存就已经爆满了，这时操作系统会变得异常卡顿，服务器也会开始不间断报警`can not keep up`，非常非常勉强的运行着。
作为低配版的windows，还是有很多东西能干的，在b站上可以找到相关的视频，甚至可以在低帧率下玩一些大型游戏（不过你只能面对着手掌大小的“巨幕”），也可以用pr,ps什么的，虽然由于性能的问题实用性不大，但是好玩啊。
总而言之，`windows arm`并不能作为主力机使用，它只能用在一些对算力要求不大的应用上，服务器什么的就很不错。以下是在平板上连接远程电脑（arm），这个个人觉得非常实用：
<iframe src="https://7llb7h-my.sharepoint.com/personal/lisiran_7llb7h_onmicrosoft_com/_layouts/15/embed.aspx?UniqueId=656de51a-8d58-4fe4-b5a0-f5f01e4f0a0e&embed=%7B%22ust%22%3Atrue%2C%22hv%22%3A%22CopyEmbedCode%22%7D&referrer=OneUpFileViewer&referrerScenario=EmbedDialog.Create" width="640" height="360" frameborder="0" scrolling="no" allowfullscreen title="WeChat_20230618091045.mp4"></iframe>
 ## miui
其实刷入windows系统对原本的miui系统影响不大，因为刷入是在分区的前提下进行的，所有安卓该是怎么样，还是怎么样，就是存储空间会小了一点。
{% asset_img 2.jpg 原本有128g的，现在分配给了windows64g %}
 ## centos
 
此系统是在安卓下容器里运行的，会存在内存分配的问题，比如有时在安卓里打游戏的时候，就会使得分配给容器的运行内存不足，导致停机。
另外的事情，就说来都是泪了。由于容器的类型是`chroot`，很多事情都干不了，如端口的开放和很多命令的使用，会报错`xxx is running in chroot.erro`，最后弄了半天，只开放了个22端口，勉强能和centos交互，但是后面使用的话，没有其他端口，什么都干不了。
{% asset_img 3.png chroot下很多命令无法使用 %}
{% asset_img 4.jpg 本地连接位于22端口的主机 %}
{% asset_img 1.jpg 使用linuxdploy启动centos系统 %}




