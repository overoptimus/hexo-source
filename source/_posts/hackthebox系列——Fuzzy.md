---
title: hackthebox系列——Fuzzy
date: 2020-03-01 17:49:51
id: 20200301174951
tags: 
	- wfuzz
	- Hack The Box
categories: 网络安全
---

打开实例后的网站。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200301175111.png" style="zoom:50%;" />

点击任何位置，都没有反应，查看源码后分析，该页面没有任何链接能够到其他地方，因此通过工具爆破目录。

这里使用dirmap工具。

```shell
python dirmap.py -i http://docker.hackthebox.eu:31822/ -lcf
```

> 注：-lcf表示的是读取配置文件，dirmap不支持通过命令行详细配置，通过github下载dirmap后，可以通过包中的README.md进行学习。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200301175523.png)

得到其中存在`/api`的目录，尝试访问，发现无法访问，那么就再api目录下再进行查找发现，扫不到东西了，那么我们换个思路，是不是在这个目录下就有我们需要的文件呐。

我们通过chrome插件`Wappalyzer`知道该网站的编程语言是PHP。

我们使用dirmap的fuzz模式，扫描api目录下的`.php`文件。

```shell
 python dirmap.py -i http://docker.hackthebox.eu:31822/api/\{dir\}.php -lcf
```

![](https://superj.oss-cn-beijing.aliyuncs.com/20200301180545.png)

访问action.php。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200301180658.png)

提示没有设置属性值，那么现在要猜解传递的属性名，我们通过`wfuzz`来猜解。

```shell
wfuzz --hh 24 -c -w /usr/share/wordlists/dirb/big.txt http://docker.hackthebox.eu:31891/api/action.php?FUZZ
```

参数解释：

| 参数 | 解释                                                         |
| ---- | ------------------------------------------------------------ |
| -c   | 输出带颜色，好看                                             |
| -w   | 指定字典                                                     |
| --hh | 如下图，wfuzz的输出中有这几项，--hh代表了chars这一列，后面设置的值为当chars值为该值时隐藏，这里表示的就是chars为24时隐藏。其他三者分别为--hc(response)、--hl(lines)、--hw(word)。 |

![](https://superj.oss-cn-beijing.aliyuncs.com/20200301181041.png)

这里详细解释一下参数`--hc/--hl/--hw--hh`。

| 参数 | 解释                     |
| ---- | ------------------------ |
| --hc | 通过返回的状态码进行过滤 |
| --hl | 通过返回的内容的行数     |
| --hw | 通过返回的内容的字数     |
| --hh | 通过返回的内容的字符数   |

我们这里选择字符是因为，观察直接访问`action.php`的返回，是一串字符，我们可以猜想当正确的属性名被请求时，返回的字符的数量肯定是不同。

这里的24是我们先运行一次不加--hh的参数的时候，发现chars这一列的值是24。

运行后得到结果。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200301181922.png)

然后访问后，提示需要正确的ID值。

因此继续通过wfuzz来进行猜测具体ID值。

```shell
wfuzz --hh 27 -c -z range,0-100 http://docker.hackthebox.eu:31891/api/action.php?reset=FUZZ
```

参数解释：

| 参数 | 解释                                                 |
| ---- | ---------------------------------------------------- |
| -z   | 设置payload的类型，这里是设置一个队列，范围在0-100。 |

![](https://superj.oss-cn-beijing.aliyuncs.com/20200301182307.png)

最后得到正确的值为20。

通过网站正常请求后，得到最后的flag。

### 总结

在这次的实验中，因为中间选择字典的问题，wfuzz一直没有爆破出正确的参数名，在仔细看了别人的wp之后，才发现自己用的和别人的不一样，但是这里就又有了一个新的问题，在进行这些敏感目录、敏感文件、参数猜测的爆破时候，选择字典的依据是什么，什么情况下选择怎样的字典，这个问题需要弄清楚。

然后贴上两个github上的字典：

fuzzdb

seclists

