---
title: v2ray的搭建过程
date: 2019-11-22 00:49:01
id: 20191122004901
categories: 杂类问题
tags:
	- v2ray
---

# 0x00 VPS

首先，第一步，是需要一个墙外的VPS，可以从很多VPS商买到。

[**bandwagonhost.com**](https://bandwagonhost.com/)

[**vultr.com**](https://www.vultr.com/?ref=7775187-4F)

bandwagonhost需翻墙后才能访问，vultr可以支付宝付款。

还有其他很多VPS商可以购买，不限于上面两家。最好买日本、香港、新加坡的，线路近，延迟低。

推荐系统的版本是`debian9+`。

# 0x01 v2ray一键脚本

用的是233boy的一键脚本，方便快捷。

```bash
bash <(curl -s -L https://git.io/v2ray.sh)
```

安装后按提示，安装、配置就可以了，傻瓜式的，很简单。

# 0x02 bbr一键脚本

```shell
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh"
chmod +x tcp.sh
./tcp.sh
```

油管上发现的，油管主视频测评推荐`BBR plus`，亲测可以。

# 0x03 WS+TLS

v2ray支持伪装成一个网站。当我们通过代理软件走`vmess`的代理的话，是翻墙的功能，当我们通过浏览器直接访问域名的话，会给我们返回伪装的网站。

话不多说，上配置。

### 域名解析

首先购买一个域名，将域名解析到你的vps的ip地址上。

我是在阿里云上购买的域名：

<img src="https://superj.oss-cn-beijing.aliyuncs.com/Screen Shot 2020-02-13 at 3.27.53 PM.png" style="zoom:50%;" />

在记录值填入你自己的VPS的IP地址，主机记录里填@。

之后就是在服务端的脚本中的配置。

### 服务端配置

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200213153513.png" style="zoom:50%;" />

选择`2.修改v2ray配置`。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200213153545.png" style="zoom:50%;" />

选择`2.修改v2ray传输协议`。

<img src="/Users/optimus/Library/Application Support/typora-user-images/image-20200213153627166.png" alt="image-20200213153627166" style="zoom:50%;" />

选择`4.WebSocket+TLS`。

后面的修改跟着提示来就行了。

# 0x04 客户端

最后就是各种客户端的下载。

`Mac OX`：[v2rayX](https://github.com/Cenmrev/V2RayX/)

`Windows`：[v2rayN](https://github.com/2dust/v2rayN)

`ios`：`shadowrocket`

`Android`：自己找去😂