---
title: 攻防世界PWN新手题——level2
date: 2019-12-20 20:10:41
id: 20191220201041
categories: CTF
tags:
	- PWN
	- Reverse
	- 攻防世界
---

<!-- more -->

# level2

## 0x00

首先通过file和checksec分析该题的附件。

![](https://superj.oss-cn-beijing.aliyuncs.com/Snip20191220_4.png)

判断该文件是32位的ELF文件，只开启了不可执行的保护。

## 0x01

开始进行反汇编，用IDA 32位。

通过左侧的函数窗口发现，只有main、vulnerable_function可用，因此进行分析，两个函数如下图：

![](https://superj.oss-cn-beijing.aliyuncs.com/Snip20191220_5.png)

![](https://superj.oss-cn-beijing.aliyuncs.com/Snip20191220_6.png)

通过分析发现，在main函数中首先调用vulnerable_function，在vulnerable_function中的缓冲区大小为88h，而read函数能够写进缓冲区100h，因此存在栈溢出。

知道是栈溢出之后就需要找到溢出点：缓冲区大小加上ebp的4个字节后，就是返回地址的位置。

![](https://superj.oss-cn-beijing.aliyuncs.com/Snip20191220_9.png)

发现在ELF文件中存在字符串“/bin/sh”，而且在主函数中最后会再次调用一次system函数，因此判断可以返回最后一次调用的位置。因此构造payload。

```python
payload = 'a' * 0x88 + 'a' * 4 + p32(0x0804849e) + p32(0x0804a024)
```

最后的`0x0804a024`为`/bin/sh`的地址。

## 0x02

最后的exp为：

![](https://superj.oss-cn-beijing.aliyuncs.com/Snip20191220_10.png)

## 0x03

![image-20191220204140234](https://superj.oss-cn-beijing.aliyuncs.com/Snip20191220_11.png)

最后获取到shell，`cat flag`得到flag。