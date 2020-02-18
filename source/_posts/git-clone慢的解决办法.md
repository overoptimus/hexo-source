---
title: git clone慢的解决办法
date: 2020-02-03 17:10:30
id: 20200203171030
categories: 杂类问题
tags:
	- git
---

<!-- more -->

# 0x00 git clone慢的问题

因为墙的原因，有些时候在`git clone`下载一些github上的库的时候会因为延迟太高，速度太慢而导致了下载失败。

# 0x01 解决办法

为`git`添加代理，前提条件是你在代理能够访问外网，并且在本地监听了`socks`的端口。

```shell
git config --global http.proxy socks5h://127.0.0.1:1081
git config --global https.proxy socks5h://127.0.0.1:1081
```

在我的电脑上，socks5监听在1081端口上。

上面的办法只在通过http和https进行clone时生效。