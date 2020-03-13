---
title: HTTP详解(六)——HTTP首部
date: 2020-03-13 22:17:26
id: 20200313221726
tags: 
	- HTTP
	- 网络基础
categories: 网络安全
---

#  HTTP报文首部

在请求中，HTTP报文由方法、URI、HTTP版本、HTTP首部字段等部分构成。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313221839.png)

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313221855.png)

在响应中，HTTP报文由HTTP版本、状态码（数字和原因短语）、HTTP首部字段3部分构成。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313221927.png)

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313221936.png)

# HTTP首部字段

### HTTP首部字段传递重要信息

HTTP首部字段是构成HTTP报文的要素之一。在客户端与服务器之间以HTTP协议进行通信的过程中，无论是请求还是响应都会使用首部字段，它能起到传递额外重要信息的作用。

使用首部字段是为了给浏览器和服务器提供报文主体大小、所使用的语言、认证信息等内容。

### HTTP首部字段结构

HTTP首部字段是由首部字段名和字段值构成的，中间用冒号分割。

比如，报文主体的对象类型：

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313222047.png)

字段值对应单个HTTP首部字段可以有多个值：

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313222110.png)

### 4种HTTP首部字段类型

根据实际用途被分为以下4种类型

- 通用首部字段（General Header Fields）

  请求报文和响应报文都会使用的首部

- 请求首部字段（Request Header Fields）

  从客户端向服务器端发送请求报文时使用的首部。补充了请求的附加内容、客户端信息、响应内容相关优先级等信息。

- 响应首部字段（Response Header Fields）

  从服务器端向客户端返回响应报文时使用的首部。补充了响应的附加内容，也会要求客户端附加额外的内容信息。

- 实体首部字段（Entity Header Fields）

  针对请求报文和响应报文的实体部分使用的首部。补充了资源内容更新时间等与实体有关的信息。

###  HTTP/1.1首部字段一览

**通用首部字段**

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313222507.png)

**请求首部字段**

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313222543.png)

**响应首部字段**

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313222557.png)

**实体首部字段**

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313222611.png)

还有一些非HTTP/1.1的首部字段，如：Cookie、Set-Cookie、Content-Dispositon。

### End-to-end首部和Hop-by-hop首部

HTTP首部字段将定义成缓存代理和费缓存代理的行为，分成2种类型。

**端到端首部（End-to-end Header）**

分在此类别中的首部会转发给请求/响应对应的最终接收目标，且必须保存在由缓存生成的响应中，另外规定它必须被转发。

**逐跳首部（Hop-by-hop Header）**

分在此类别中的首部只对单次转发有效，会因通过缓存或代理而不再转发。HTTP/1.1和之后版本中，如果要使用hop-by-hop首部，需提供Connection首部字段。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313222840.png)

除以上外，都是端到端首部。