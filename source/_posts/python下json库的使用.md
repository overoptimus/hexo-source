---
title: python下json库的使用
date: 2020-02-04 11:33:23
id: 20200204113323
categories: python
tags:
	- 第三方库
	- python
---

# 0x00 json介绍

JSON（JavaScript Object Notation）是一种轻量级的数据交换格式，易于人阅读和编写。

JSON常用做网站异步请求的数据交换，网站异步请求，对服务器进行请求后，服务端进行处理后，将处理后的结果通过JSON格式传回给客户，客户端经过解析，表现出来。

在一些程序的编写过程中，通常也通过JSON来进行配置数据的存储，以此方便程序的编写。

# 0x01 python下的json

在python中有一个json的库，提供了对json文件的使用。

要使用json库，需要在开始导入json库：`import json`。

# 0x02 json使用

导入json库之后，最常见的两个函数是：

| 函数       | 描述                   |
| ---------- | ---------------------- |
| json.dumps | 将python对象解析为json |
| Json.loads | 将json解析为python对象 |

这两个方法将python对象和json字符串进行相互的转化。在读取json文件后，通过`loads`解析为python对象，能够使用对象的方法。在处理完数据之后，将python对象解析为json字符串，方便存储。

### python对象类型和json类型转化对照表

| python           | json   |
| ---------------- | ------ |
| dict             | object |
| list, tuple      | array  |
| str, unicode     | string |
| int, long, float | number |
| True             | true   |
| False            | false  |
| None             | null   |

