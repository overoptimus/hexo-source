---
title: python下r''、b''、f''、u''
date: 2020-02-18 17:32:11
id: 20200218173211
tags: 
	- python
categories: python
---

# r''

转义字符以原样输出，不做转义字符识别。如`\n`，输出`\n`，不是换行符。

```python
print(r'hahahahahahaha\n')
```

输出：

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200218175337.png" style="zoom:50%;" />

可以发现原样输出了`\n`。

# b''

这里有python2和python3的区别。

在python3中，字符串的类型有`str`和`bytes`。

```python
a = '111'
type(a)
# 输出str
type(a.encode())
# 输出bytes
type(a.decode())
# 输出str没有decode的方法
```

在python2中，字符串的类型有`str`和`unicode`。

```python
a = '111'
type(a)
# 输出str
type(a.encode())
# 输出str
type(a.decode())
# 输出unicode
```

我们可以发现，python2中的str就是python3中的str.encode()后的，而python3中的str就是python2中str.decode()后的。也就是说，在两个版本中默认的str有如下关系。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200218182318.png" style="zoom:50%;" />

因此，在python3中，`b'111'`等同于`'111'.encode()`。

# u''

表示该字符串是unicode编码的，python3的默认编码方式就是unicode。

> 这里要说一下，unicode是一种标准，是字符集，符合这一标准的编码方式有utf-8、utf-16、utf-32等。

# f''

格式化字符串的输入方式，见下面的例子。

```python
a = 'name'
b = '0pt1mus'

'my {} is {}'.format(a, b)
f'my {a} is {b}'
```

上面的两种格式化字符串都没有问题，但`f''`是不是更加方便呐。