---
title: schtasks /query显示错误：无法加载列资源
date: 2021-01-26 21:24:22
id: 20210126212422
tags:
	- 小技巧
categories: 网络安全
---

操作系统：win 7

该原因是编码的不支持，通过chcp查看编码，936会导致无法加载列资源，修改为437即可。

```powershell
chcp 437
```
![在这里插入图片描述](https://superj.oss-cn-beijing.aliyuncs.com/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzcxMzgwMA==,size_16,color_FFFFFF,t_70.png)

原因分析：刚开始认为就是因为编码的问题，不支持中文的编码，但是之后在win server 2012 r2上进行操作时，查看编码号也是936，所以应该不是编码的问题，可能就是在win7上出现的问题。

