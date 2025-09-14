---
title: IDM破解攻略
date: 2023-09-28 23:04:26
tags:
- IDM
- 解锁
- 技术
cover: 0.jpg
background: url(0.jpg)
---
{% note danger modern %}
单纯出于学习研究目的，禁止用于商业用途及任何途径的传播。方法参考自网络，可能存在时效性，不保证一定可用。
{% endnote %}
# 修改计算机host文件
用“win+R”唤出运行面板，输入以下内容：
```cmd
C:\Windows\system32\drivers\etc
```
{% asset_img 1.jpg 运行 %}
回车，然后找到`hosts`文件：
{% asset_img 2.jpg 运行 %}
右键，按顺序选择`属性`-`安全`-`编辑`.在出现的框中，给`Users`修改的权限。（完全控制也可以）
{% asset_img 3.jpg 赋予权限 %}
然后回到文件夹，使用记事本打开`hosts`文件，在最下面加入以下内容：
```hosts
127.0.0.1 http://registeridm.com
127.0.0.1 http://www.registeridm.com
127.0.0.1 http://www.internetdownloadmanager.com
```
保存即可。
# 激活IDM
{% note danger modern %}
请先断开网络
{% endnote %}
打开IDM，进入输入序列号的界面，前面三项乱写就可以，序列号可以选择以下一个填入：
```序列号
OS5HG-K90NH-SXOGT-7JYEZ
R2C1T-O0KQO-JAVU2-4MMYP
M2A16-47AAW-6NLYP-V1E0J
IZO7M-360FW-QY1XP-AWLPN
```
点击激活即可。如果弹出系统安全提示，一直选继续就好了。
然后IDM会重启，进去就会发现IDM已经是激活状态了。
