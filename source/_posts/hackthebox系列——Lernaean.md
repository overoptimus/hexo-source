---
title: hackthebox系列——Lernaean
date: 2020-02-29 22:25:00
id: 20200229222500
tags: 
	- Hydra
	- Hack The Box
categories: 网络安全
---

现在开始在`Hack The Box`中练习，希望可以对您有所帮助。

------

这道题目是Challenges->Web下的Lernaean。我们百度搜索Lernaean，得到的第一个百度百科是水蛇许德拉(Lernaean Hydra)，因此我们可以考虑是通过Hydra进行爆破。

打开实例化后的网页：

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200301171013.png" style="zoom:50%;" />

可以发现只显示一个输入框和一个提交按钮，查看源码，发现是通过POST方式请求。

现在我们尝试提交一个请求观察返回的变化。

填入1，点击Submit：

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200301171214.png" style="zoom:50%;" />

观察到返回页面中出现`Invalid password!`的关键字样，因此，可以开始通过Hydra爆破密码。

```shell
hydra -t 60 -l Administrator -P /usr/share/wordlists/rockyou.txt -o out.txt -f -s 31927 docker.hackthebox.eu http-post-form "/:password=^PASS^:Invalid password"
```

解释其中的参数

| 参数                               | 意义                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| -t                                 | 设置线程                                                     |
| -l                                 | 设置用户名（因该处提交的表单只验证密码，因此用户名设置与结果无关，所以可以随意设置，并符合hydra的规范） |
| -P                                 | 设置字典位置                                                 |
| -o                                 | 结果输出位置                                                 |
| -f                                 | 匹配到一个之后停止                                           |
| -s                                 | 若不是默认端口，设置端口                                     |
| http-post-form                     | 以post请求进行爆破                                           |
| docker.hackthebox.eu               | 目标域名                                                     |
| /:password=^PASS^:Invalid password | 通过`:`分割开，`/`表示爆破的目录；`password=^PASS^`表示post请求表单中的数据，`^`包裹起来的`PASS`是字典中的条目，`Invalid password`表示的是登录失败的特征字符串 |

运行得到结果：

![](https://superj.oss-cn-beijing.aliyuncs.com/截屏2020-03-01下午5.26.35.png)

我们填入密码，提交后，得到返回：

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200301172826.png" style="zoom:50%;" />

没有发现任何关于flag的信息，因此想到通过bp进行抓包重放。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/截屏2020-03-01下午5.32.53.png" style="zoom:50%;" />

在响应包中找到flag。

