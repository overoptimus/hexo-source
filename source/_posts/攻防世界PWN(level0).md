---
title: 攻防世界PWN（level0）
date: 2019-12-18 22:18:57
id: 20191218221857
categories: CTF
tags: 
	- PWN
	- 栈溢出
	- 攻防世界
---

<!-- more -->

# level0

## 0x00

首先，进行通过file判断附件文件类型。

![image-20191218220110553](攻防世界PWN(level0)/image-20191218220110553.png)

判断是ELF文件。这时就可以通过checksec判断文件的保护措施。

![image-20191218220211791](攻防世界PWN(level0)/image-20191218220211791.png)

得到只开启了执行保护，地址随机化、栈保护都没有开启。

## 0x01

开始IDA逆向分析。

![image-20191218220426947](攻防世界PWN(level0)/image-20191218220426947.png)

在其中发现了main、vulnerable_function、callsystem函数逐个查看这三个函数。

![image-20191218220524755](攻防世界PWN(level0)/image-20191218220524755.png)

![image-20191218220601881](攻防世界PWN(level0)/image-20191218220601881.png)

![image-20191218220616595](攻防世界PWN(level0)/image-20191218220616595.png)

发现该题中用到了write、read函数，google一下发现read函数是往内存里面写，而且发现大小为0x200大小，比0x80大的多，所以想到栈溢出，而且有callsystem函数（地址为0x400596）能够直接得到shell。

最终，确定了通过栈溢出执行callsystem从而得到shell。

## 0x02

编写exp。

![image-20191218221333336](攻防世界PWN(level0)/image-20191218221333336.png)

## 0x03

得到flag。

![image-20191218221438956](攻防世界PWN(level0)/image-20191218221438956.png)

