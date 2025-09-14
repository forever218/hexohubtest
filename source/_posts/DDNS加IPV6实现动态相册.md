---
title: DDNS加IPV6实现动态相册
date: 2025-03-04 21:48:31
tags:
- blog
- 摄影
- 技术
cover: 0.jpg
background: url(0.jpg)
publish_location: 山西省-太原市-尖草坪区
---
&nbsp; &nbsp; &nbsp; 一直想有一个网络相册，之前建的一个纯静态站点效果并不如人意，每次更新都非常麻烦。{% btn 'https://2am.top/photo2/  ',前往旧版相册,far fa-hand-point-right,blue larger %}后来也试过本地部署加内网穿透的方式，来实现动态的相册应用（主打一个白嫖，懒得租服务器）。不过这种方式相当不稳定（即使是在电脑一直保持开机的状态），FRP服务商所提供的免费服务本身就经常抽风，出现节点异常的情况。
最主要的问题在于，自己没有一个公网IP（IPV4）。这几天突然想到，既然IPV4难以获得，那IPV6呢？！真的眼前一亮的感觉，因为运营商都会给每个带宽用户分配一个`公网`的IPV6地址，`公网`非常重要，意味着你的设备可以直接被外网访问到，而类似于校园网这样的网络，即使提供了IPV6，却是内网的地址。
{% asset_img 1.jpg  %}
上图中，`fe80::f922:5cd2:d491:4a77%3`就是校园网分配的内网IPV6（外部无法访问）
{% asset_img 2.jpg  %}
上图中，`240e:424:782:5668:501d:c187:61fd:ab53`就是移动数据分配的动态公网IPV6地址，外部网络可以访问，并且这个地址会不定期的变化。
那么现在要做的就是，将本地服务器绑定域名，再将域名解析到IPV6地址上。上面说过，运营商分配的IPV6地址是动态的，所以需要一个DDNS（动态域名解析），来实时更新域名解析。

# 流程

## 1.本地服务器绑定域名
很简单，填几个空的事：
{% asset_img 3.jpg  %}
{% asset_img 4.jpg  %}

## 2.域名动态解析到IPV6地址
以DNSPOD为例：首先在解析的域名下添加一条AAAA记录，记录值填写运营商分配的IPV6地址。
{% asset_img 5.jpg  %}
在`我的账号`-`API密钥`-`DNSPOD TOKEN`中，新建一个token，记录下id和token：
{% asset_img 6.jpg  %}
回到本地，新建一个python脚本，用于更新实时的IPV6地址到DNSPOD：
```python
import requests
import json

# Dnspod API 配置
DNSPOD_ID = "你的ID"
DNSPOD_TOKEN = "你的token"
DOMAIN = "2am.top"  # 例如 "example.com"
SUBDOMAIN = "img"  # 例如 "blog" (表示 blog.example.com)
LOGIN_TOKEN = f"{DNSPOD_ID},{DNSPOD_TOKEN}"

# 获取当前 IPv6 地址
def get_ipv6():
    try:
        response = requests.get("https://api64.ipify.org?format=json")
        return response.json().get("ip")
    except Exception as e:
        print(f"获取 IPv6 失败: {e}")
        return None

# 获取 DNS 记录 ID
def get_record_id():
    url = "https://dnsapi.cn/Record.List"
    data = {
        "login_token": LOGIN_TOKEN,
        "format": "json",
        "domain": DOMAIN,
        "sub_domain": SUBDOMAIN
    }
    response = requests.post(url, data=data)
    result = response.json()
    if result["status"]["code"] == "1":
        return result["records"][0]["id"]
    else:
        print(f"获取 DNS 记录 ID 失败: {result}")
        return None

# 更新 Dnspod 的 DNS 解析
def update_dns(ipv6):
    record_id = get_record_id()
    if not record_id:
        return

    url = "https://dnsapi.cn/Record.Modify"
    data = {
        "login_token": LOGIN_TOKEN,
        "format": "json",
        "domain": DOMAIN,
        "record_id": record_id,
        "sub_domain": SUBDOMAIN,
        "record_type": "AAAA",  # IPv6 解析
        "record_line": "默认",
        "value": ipv6
    }
    
    response = requests.post(url, data=data)
    result = response.json()
    
    if result["status"]["code"] == "1":
        print(f"成功更新 DNS 记录: {ipv6}")
    else:
        print(f"更新失败: {result}")

# 主函数
def main():
    ipv6 = get_ipv6()
    if ipv6:
        update_dns(ipv6)

if __name__ == "__main__":
    main()
```
然后，使用windows的任务计划程序，定时执行这个脚本，即可实现动态解析。
{% asset_img 7.jpg  %}

&nbsp; &nbsp; &nbsp; 目前，我使用的图库软件是piwigo，安装很方便，本地部署一个服务器和数据库和其连接（80HTTP端口）即可。{% btn 'http://img.2am.top',前往相册,far fa-hand-point-right,blue larger %}，懒得部署SSL证书了。
{% asset_img 8.jpg  %}
&nbsp; &nbsp; &nbsp; 当然了，这也不是完美的解决方案，这得要求我电脑一直开着，并且连着手机的热点才能工作，而且还要运行自动任务（DDNS）和启动服务器。对访问者也同样有要求，需要在支持公网IPV6的环境下才能访问（比如你连着校园网就无法访问）。
所以img.2am.top目前处于一个随缘在线的状态，如果你现在无法访问，不妨调个闹钟，过一会再来，就会发现说不定还是无法访问🤪。
