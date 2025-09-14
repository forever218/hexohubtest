---
title: Stablediffusion及AI绘画
date: 2023-04-16 22:42:48
tags: 技术
cover: 12.jpg
description: 一个用python写的开源AI绘画项目，很有意思。
swiper_index: 4 #置顶轮播图顺序，非负整数，数字越大越靠前
background: url(12.jpg)
---
上个星期在读Github发给我的周报的时候，看见了一个叫Stablediffusion的项目，嗯？稳定扩散？好奇心一下上来了。然后又看到这个项目居然有64.9k的stars和12.2k的forks，怀着试试就逝世的心理，跑去浅浅的研究了一下。
这是一个用python写的应用，现在非常火，用于AI绘画。其中[stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui)  是基于Gradio库的浏览器界面，说白了就是可以用它做一个AI绘图网站。
有一说一，安装是真的麻烦，一步几个报错，不是`erro`就是`warning`，那几天差点给我干崩溃。上网找了一堆教程，东拼西凑总算是搞定了。其实根本的原因在于，这个项目大量的第三方依赖和库文件都是国外的，安装起来非常慢，而且一但响应久了，python直接给你cut掉，那叫一个绝望，一个几mb的包能折腾一天。
以下是我的具体步骤，当然，每个电脑情况和网络环境不同，有极大几率会出不同的问题，我的教程仅供参考。
`ai绘图对电脑硬件有很大要求，特别是显卡。我的笔记本是3070ti，画1000×1000的图片时显卡已经跑满，再大的图片就画不出来了`
## 安装python
去官网[python](https://www.python.org/)下载对应的安装包，一路傻瓜式的安装，一直点next就好了。
{% note warning modern %}
别忘了勾选`add to path`选项，方便以后直接在cmd运行python.
{% endnote %}
安装完成后在`cmd`中输入`python`，若出现类似一下回应，即代表安装成功：
{% asset_img 0.png python install successfully %}
## 克隆主仓库到本地
建议将这个主目录克隆到c盘之外的地方，因为将来加上模型，大小可能会超过20G。
如：在d盘右键使用`git bush`：
```bash
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui
```
等待即可。如果长时间卡住，可以试试魔法上网。
## 安装pytorch
去[官网](https://pytorch.org/get-started/locally/)选择对应自己型号的软件，一般来说，选择stable-windows-pip-python-cuda即可，其中，cuda可以选用最新的版本（如果你懒得查的话）。
复制给出的命令，例如：
```cmd
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
```
即可通过pip安装pytorch.
然后，不出意外的话，已经出意外了。😁😁😁
会有各种各样的报错，最后告诉你pytorch安装失败。当然如果你足够幸运，成功了，就可以跳过这一节；如果这一步失败了，那么恭喜你，获得了一个新的安装方法：
上面我说了，安装失败十有八九是网络的问题，我们只需要使用国内镜像进行本地安装即可解决。
### 本地安装pytorch
按理来说，报错通常是告诉你说`xxxx installed unsseccfully`，前面那个就是安装失败的包名，这时将包名记录下来，到网站https://mirror.sjtu.edu.cn/pytorch-wheels/cu117/?mirror_intel_list 找到对应的`.whl`文件，下载下来。
比如说你将它下载到了`/桌面/Stablediffusion/`，那么就在该文件夹下使用cmd命令`pip3 install 包名.whl`,即可完成本地安装。
{% asset_img b.png 报的错大概长这样 %}
### 检验安装
完成之后，你可以执行一下代码来测试是否安装成功：
```cmd
import torch
x = torch.rand(5，3)
print(x)
```
如果输出结果类似于:
```cmd
tensor([[0.3380，0.3845， .3217]
[0.8337， 0.9050，.26507
[.2979，0.7141. 0.90697
[0.1449，0.1132，0.13757
[0.4675，0.3947，0.142677)
```
说明安装成功。
## 安装第三方依赖
stablediffusion需要许多第三方依赖才可以正常运行。进入之前克隆到本地的工作主目录，执行：
```cmd
python -m pip install -r requirements.txt
```
同样的，这一步非常折磨人，卡非常非常久，一堆包没有正确安装，我们依旧需要到网站https://mirror.sjtu.edu.cn/?mirror_intel_list 下载对应的包，进行手动本地安装。
超级超级折磨人！！！最后能不能挺过这一步，完全取决于你对于折腾这件事的热情有多少了。这一步我卡了整整2天。
## 模型安装
有了主体框架，现在就需要安装模型，来决定图片的生成方式。可以去大名鼎鼎的c站：https://civitai.com/ 下载感兴趣的模型文件，将它放进主工作目录`models/Stable-diffusion`里。
## 尾声
该项目的安装到此就结束，现在我们可以试着玩耍了。在主目录下，执行：
```cmd
python launch.python
```
即可启动。不出意外，应该能收到如下回馈：
{% asset_img 1.png python 回馈 %}

将地址`127.0.0.1:7860`填入浏览器并访问，便能访问自己搭建的ai绘图网站了。点击`gernerate`开始你的生成！
{% asset_img 2.png python 的网页 %}
PS：这个项目可以自己加很多算法、模型和风格，使你的ai画出来的图片独一无二；也有汉化、自定义主题这些对于webui的优化。这些都可以在网上搜到，也有各路大佬的教程，可以直接去折腾折腾。
（有很多东西我现在都不知道是干嘛的，但是看起来很牛逼）
# 我想说的
最近几年，有关ai绘画的事情那真是一波未平一波又起，被炒的沸沸扬扬，有人极力推崇，有人极力反对。有人对之爱不释手，有人看到就烦。其实我觉得，对于这件事情来说，完全没有必要去追究它到底好不好，因为它既然出现了，就有其合理的一面。很多画师说ai抢了自己饭碗，但实际上ai画的和人画的还是很有差距的，我看了很多很多ai画的画，虽然很逼真，很细腻，却总能感觉出哪里有问题，如果真要细说的话，那就是——ai画的少了灵气。
说着可能比较玄乎，事实上就是这样的。ai不同于人类，作画的时候没有情感，完全由算法来决定作品的生成方式，这就决定了ai不能分辨出这张图片想表达什么样的情感，暗含了什么样的意蕴，所以画出来的图就让人看着哪里不对劲。而人是有感情的，有思想的，在作画时就会自然而然的将其融入作品里，能够让别人引起共鸣。
诚然，随着科技的发展，ai肯定会越来越智能，使得其带有一定的情感成为可能。到时，就真的分不清楚，是人类模仿ai，还是ai模仿人类了。
# 附录
这里另外附上我用GF3模型生成的一些图片：
{% asset_img 3.png ai %}
{% asset_img 4.png ai %}
{% asset_img 5.png ai %}
{% asset_img 6.png ai %}
{% asset_img 7.png ai %}
{% asset_img 8.png ai %}
{% asset_img 9.png ai %}
{% asset_img 10.png ai %}
{% asset_img a.png 过程 %}