---
title: HTTP详解(二)——简单的HTTP协议
date: 2020-03-09 21:39:40
id: 20200309213940
tags: 
	- HTTP
	- 网络基础
categories: 网络安全
---

# HTTP协议用于客户端和服务端之间的通信

HTTP协议和TCP/IP协议族内的其他众多协议相同，用于客户端和服务器之间的通信。

请求访问文本或图像等资源的一端称为客户端，而提供资源响应的一端称为服务器端。

有时候，按实际情况，两台计算机作为客户端和服务器端的角色有可能会互换。但就仅从一条通信线路来说，服务器端和客户端的角色是确定的，而用HTTP协议能够明确区分那端是客户端，哪端是服务器端。

# 通过请求和相应的交换达成通信

请求报文是由请求方法、请求URI、协议版本、可选的请求首部字段和内容实体构成的。

响应报文基本上由协议版本、状态码（表示请求成功或失败的数字代码）、用以解释状态码的原因短语、可选的响应首部字段以及实体主体构成。

# HTTP是不保存状态的协议（对用户的登陆状态不进行保存）

HTTP是一种不保存状态，即无状态（stateless）协议。HTTP协议自身不对请求和响应之间的通信状态进行保存。也就是说在HTTP这个级别，协议对于发送过的请求或响应都不做持久化处理。

使用HTTP协议，每当有新的请求发送时，就会有对应的新响应产生。协议本身并不保留之前一切的请求或响应报文的信息。这是为了更快地处理大量事务，确保协议的可伸缩性，而特意把HTTP协议设计成如此简单的。

HTTP/1.1虽然是无状态协议，但为了实现期望的保持状态功能，于是引入了Cookie技术。有了Cookie再用HTTP协议通信，就可以管理状态了。

# 请求URL定位资源

在WWW上，每一信息资源都有统一的且在网上唯一的地址，该地址就叫URL（Uniform Resource Locator,统一资源定位符），它是WWW的统一资源定位标志，就是指网络地址。

URL由三部分组成：资源类型、存放资源的主机域名、资源文件名。也可认为由四部分组成：协议、主机、端口、路径。

> protocal://hostname[:port]/path/[;parameters][?query]#fragment
>
> 注：带方括号[]的为可选项，默认的port为80

# 告知服务器意图的HTTP方法

#### GET：获取资源

GET方法用来请求访问已被URI识别的资源。指定的资源经服务器端解析后返回响应内容。也就是说，如果请求的资源是文本，那就保持原样返回，如果是向CGI（Common Gateway Interface，通用网关接口）那样的程序，则返回经过执行后的输出结果。

#### POST：传输实体主体

POST方法用来传输实体的主体。

虽然用GET方法也可以传输实体的主体，但一般不用GET方法进行传输，而是用POST方法。虽然POST的功能与GET很相似，但POST的主要目的并不是获取响应的主体内容。

#### PUT：传输文件

PUT方法用来传输文件。就像FTP协议的文件上传一样，要求在请求报文的主体中包含文件内容，然后保存到请求URI指定的位置。

但是，鉴于HTTP/1.1的PUT方法自身不带验证机制，任何人都可以上传文件，存在安全性问题，因此一般的Web网站不使用该方法。若配合Web应用程序的验证机制，或架构设计采用REST（Representational State Transfer，表征状态转移）标准的同类Web网站，就可能会开放使用PUT方法。

#### HEAD：获得报文首部

HEAD方法和GET方法一样，只是不返回报文主体部分。用于确认URI的有效性及资源更新的日期时间等。

#### DELETE：删除文件

DELETE方法用来删除文件，是与PUT相反的方法。DELETE方法按请求URI删除指定的资源。

但是，HTTP/1.1的DELETE方法本身和PUT方法一样不带验证机制，所以一般的Web网站也不使用DELETE方法，当配合Web应用程序的验证机制，或遵守REST标准时还是有可能开放使用的。

#### OPTIONS：询问支持的方法

OPTIONS方法用来查询针对URI指定的资源支持的方法。

#### TRACE：追踪路径

TRACE方法是让Web服务器端将之前的请求通信环回给客户端的方法。

#### CONNECT：要求用隧道协议连接代理

CONNECT方法要求在与代理服务器通信时建立隧道，实现用隧道协议进行TCP通信。主要使用SSL（Secure Sockets Layer，安全套接层）和TLS（Transport Layer Security，传输层安全）协议把通信内容加密后经网络隧道传输。

> 重要的是GET方法和POST方法的区别

# 持久连接节省通信量

HTTP通信一次，进行一次TCP的连接与断开，若HTTP传输的信息很多，每次都要进行TCP连接与断开，造成了无谓的通信量开销。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200309215518.png)

## 持久连接

为解决上述的TCP连接问题，HTTP/1.1和一部分的HTTP/1.0相处了持久连接（HTTP Persistent Connections，也称为HTTP keep-alive或HTTP connection reuse）的方法。持久连接的特点是，只要任意一端没有明确提出断开连接，则保持TCP连接状态。

持久连接的好处在于减少了TCP连接的重复建立和断开造成的额外开销，减轻了服务器端的负载。另外，减少开销的那部分时间，使HTTP请求和响应能够更早地结束，这样Web页面的显示速度也就相应提高了。

在HTTP/1.1中，所有的连接默认都是持久连接，但在HTTP/1.0内并未标准化。虽然有一部分服务器通过非标准的手段实现了持久连接，但服务器端不一定能够支持持久连接。毫无疑问，除了服务器端，客户端也需要支持持久连接。

## 管线化

持久连接使得多数请求以管线化（pipelining）方式发送称为可能。从前发送请求后需等待并收到响应，才能发送下一个请求。管线化技术出现后，不用等待响应亦可直接发送下一个请求。这样就能够做到同时并行发送多个请求，而不需要一个接一个地等待响应了。

# 使用Cookie的状态管理

HTTP是无状态协议，它不对之前发生过的请求和响应的状态进行管理。也就是说，无法根据之前的状态进行本次的请求处理。

为了保留无状态协议这个特征的同事又要解决类似的矛盾问题，于是引入了Cookie技术。Cookie技术通过在请求和响应报文中写入Cookie信息来控制客户端的状态。

Cookie会根据从服务器端发送的响应报文内的一个叫做Set-Cookie的首部字段信息，通知客户端保存Cookie。当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入Cookie值后发送出去。

服务器端发现客户端发送过来的Cookie后，会去检查究竟是从哪一个客户端发来的连接请求，然后对比服务器上的记录，最后得到之前的状态信息。