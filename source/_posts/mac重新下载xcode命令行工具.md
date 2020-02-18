---
title: mac重新下载xcode命令行工具
date: 2020-02-04 20:56:12
id: 20200204205612
categories: 杂类问题
tags:
	- MAC
---

<!-- more -->

# 0x00 遇到问题

在使用`npm install`时报错

```shell
gyp: No Xcode or CLT version detected!
```

# 0x01 重新下载安装xcode命令行

```shell
rm -rf /Library/Developer/CommandLineTools 
xcode-select --install
```

若权限不够，加`sudo`。