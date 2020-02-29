---
title: hackthebox系列——注册
date: 2020-02-29 20:30:20
id: 20200229203020
tags: 
	- hack the box
categories: 网络安全
---

最近在看杨老师的网络安全自学篇系列，再看到后面的时候，杨老师介绍了一个在线的靶场：`Hack The Box`，亲身体验了一下，感觉是一个检验和提升自己的渗透能力，因此介绍给大家。

[杨老师网络安全专栏]: https://blog.csdn.net/Eastmount/category_9183790.html
[Hack The Box官网]: https://www.hackthebox.eu 

# 0x00 简介

`Hack The Box`是一个在线平台，可让您测试和提高网络安全技能。负责任地使用它，不要破坏您的同事...

可以理解该平台是一个在线的靶场，通过类似CTF的形式出题，不过更加接近于真实的环境。通过练习可以学习到各种各样的知识，各种工具的使用。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200229204250.png" style="zoom:50%;" />

# 0x01 注册

首先打开官网，向下翻动一小截，发现`join`。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/1.png" style="zoom:50%;" />

点击，进入填写邀请码的界面。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200229205008.png" style="zoom:50%;" />

我们可以通过`F12`或在url前加`view-source:`来查看源码。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200229205735.png" style="zoom:50%;" />

可以发现其中引用了`/js/inviteapi.min.js`，访问`https://www.hackthebox.eu/js/inviteapi.min.js`

可以看到js源码。

![](https://superj.oss-cn-beijing.aliyuncs.com/image-20200229210010434.png)

观察到其中有`makeInviteCode`，我们直接在console中执行该方法。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200229210133.png"  />

得到base64编码的字符串，通过在线base64解码网站得到：![](https://superj.oss-cn-beijing.aliyuncs.com/20200229210243.png)

需要我们提交一个POST请求，我们这里通过`HackBar`模拟POST请求。<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200229215037.png" style="zoom:50%;" />

得到code，发现是base64编码后的，因此再次解码，得到邀请码，填入后，进入注册界面。<img src="https://superj.oss-cn-beijing.aliyuncs.com/image-20200229215300030.png" style="zoom:50%;" />

最后的验证码是google的人机验证，你懂得，可查看我的个人博客。

然后去填入的邮箱，查收邮件，确认注册，你就可以愉快的进行练习了。可以在challenges中查看题目。

# 0x02 总结

在找到引用的js文件后，分析代码时，不够仔细认真，没有发现其中明显的`makeInvateCode`，以后在练习中，一定要耐下心来，审查好每一个关键要素，不要遗漏。