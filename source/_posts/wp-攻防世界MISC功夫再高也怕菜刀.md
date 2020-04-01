---
title: (wp)攻防世界MISC功夫再高也怕菜刀
date: 2020-04-01 23:14:42
id: 20200401231442
tags: 
	- MISC
	- 攻防世界
categories: CTF
---

攻防世界MISC类新手题的最后一道题，感觉思路啥的有些绕，不过用到的知识点还是很多的，记录一下。

这个wp参考网上大佬文章，自己是真的想不出来这种骚操作，还是太菜。

# 分析

首先将附件下载下来，发现是一个`.pcapng`，是wireshark抓包后保存的文件。我们通过wireshark打来。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200401232846.png)

可以发现抓取的包中协议类型有TCP、HTTP、ARP，我们可以确定这是一个网页的请求。

我们通过搜索关键字`flag`，可以找到几个有flag关键字的包，逐个查看其TCP流，可以找到几个上传的包，在编号为1150的包的TCP流中，我们可以在其中找到已`FFD8`开头，`FFD9`结尾的十六进制串，这里是个知识点，这种开头结尾的十六进制串，是一个图片，我们将其复制出来，通过`010Editor`新建一个空文件，以十六进制文本粘贴，保存成一个`.jpg`文件。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200401234403.png)

打开，发现是一个字符串。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200401234441.png)

将其填入flag，发现不是flag。因此我们又要想了，这个字符串到底是用在哪里呐。

我们通过`binwalk`，发现其中还有一个zip文件。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200401234614.png)

通过`foremost`，分解出隐藏的文件。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200401234704.png)

打开分离出的压缩文件要求输入密码，填入`Th1s_1s_p4sswd_!!!`，得到flag文件。

# 总结

这道题中的工具都知道，但是就是没有想到，还是做得题太少，脑子动的太少，归根到底，还是太菜。遇到问题一定要多尝试，把知道的方法都是一遍，总会有成功的时候，再不成功那就是知识面有短缺了，然后网上找一下大佬的wp，多学习。