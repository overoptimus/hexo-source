---
title: hackthebox系列——Cartographer
date: 2020-03-01 17:37:46
id: 20200301173746
tags: 
	- Hack The Box
categories: 网络安全
---

这是一道很简单的sql注入的题目，打开实例后的网页，就可以看到一个登陆的界面。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200301174008.png)

尝试一下万能的登陆绕过注入。发现成功绕过登陆。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200301174236.png" style="zoom:50%;" />

但是任然没有flag，但是观察url

```
http://docker.hackthebox.eu:31960/panel.php?info=home
```

指定了info的值为home，这个时候就可以换一个参数，是不是就出来flag了呐。

成功。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/image-20200301174457293.png" style="zoom:50%;" />

