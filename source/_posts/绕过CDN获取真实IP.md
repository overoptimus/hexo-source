---
title: 绕过CDN获取真实IP
date: 2020-03-29 10:21:19
id: 20200329102119
tags: 
	- CDN绕过
categories: 网络安全
---

# CDN介绍

CDN（Content Delivery Network，即内容分发网络)。CDN是构建在现有网络基础之上的只能虚拟网络，依靠部署在各地的边缘服务器，通过中心的负载均衡、内容分发、调度等功能模块，使用户就近获取所需内容，降低网络拥塞，提高用户访问响应速度和命中率。

# 域名解析过程

传统访问：用户访问域名->解析IP->访问目标主机

套用CDN后：用户访问域名->CDN节点->真实IP->目标主机

目前有部分的CDN服务商也提供了WAF的功能，对一些恶意的流量进行拦截。

# CND检测方法

#### 全球ping

通过全球各个地方对目标网站进行ping，观察返回的IP地址手否相同，若每个地区的IP地址不相同，则说明存在CDN。

利用网站：http://ping.chinaz.com/

#### nslookup

通过`nslookup`工具来判断是否有使用CDN。

![](https://superj.oss-cn-beijing.aliyuncs.com/截屏2020-03-29上午10.53.04.png)

若套用了CDN的话，和baidu.com的结果类似，在非权威回到中有多个解答；若没有套用CDN的话，和superj.site的结果类似，在非权威回答中只有一个解答。

# 绕过CDN找真实IP

#### 通过子域找真实IP

通常使用CDN服务会产生服务费用，网站管理员一般只会给重要的业务和主站使用CDN，而访问较少和不重要的业务不会使用CDN，而且一般情况下，主站和子站会在一个服务器上，因此可以通过子域名来找真实IP。

##### 收集子域名的方法：

```
# google hacker语法
site:baidu.com

# 在线工具
https://phpinfo.me/domain/
http://tool.chinaz.com/subdomain/
https://securitytrails.com/
https://dnsdb.io/zh-cn/
```

#### 通过历史DNS记录找真实IP

查找DNS的解析记录，在没使用CDN之前，DNS解析的是网站的真实IP。

可以使用如下网站进行查询：

```
https://x.threatbook.cn/
https://ipchaxun.com/
https://viewdns.info/iphistory/
```

#### 通过邮箱找真实IP

我们在访问目标网站的时候，会先去找CDN，但是如果网站主动和客户端通信的话，不会使用CDN，那么我们看到的源地址就是目标网站的真实IP。

那么什么时候网站会主动和客户通信呢？我们在注册用户、修改密码、找回密码时，服务器会给用户发送邮件，若此时邮件服务器和网站服务器在一起，那么我们就可以获取到网站的真实IP。若两者不在一起，那么绑定IP地址后，可能会造成无法访问目标网站。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200329120932.png)

![image-20200329121007955](/Users/optimus/Library/Application Support/typora-user-images/image-20200329121007955.png)

这里的地址是一个ipv6的地址。

#### 通过探针找真实IP

如果可以在网站上发现phpinfo.php等文件时，我们可以通过phpinfo()等方法获得真实IP。

在phpinfo()返回的信息中，SERVER_NAME参数中反悔了真实的IP地址。

#### 通过网站漏洞找真实IP

网站若发现存在XSS、命令执行、上传文件、文件包含等漏洞，我们可以直接通过上传探针文件，执行命令来获取网站的真实IP。

#### 通过网络空间引擎搜索找真实IP

```
https://fofa.so/
https://www.zoomeye.org/doc
https://www.shodan.io/
```

只需要输入：`title:"网站的title关键字"`或者`body:"网站的body特征"`就可以找出这些引擎收录的有这些关键字的ip域名，很多时候都可以获得网站的真实IP。





