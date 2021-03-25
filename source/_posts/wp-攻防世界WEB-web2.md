---
title: wp-攻防世界WEB-web2
date: 2021-03-22 22:36:17
id: 20210322223617
tags:
	- CTF
	- writeup
categories: 网络安全
keywords:
description:
---

这是一道简单的解密题，首先放上题目源码。

![image-20210322223917635](https://superj.oss-cn-beijing.aliyuncs.com/image-20210322223917635.png)

```php
<?php
$miwen="a1zLbgQsCESEIqRLwuQAyMwLyq2L5VwBxqGA3RQAyumZ0tmMvSGM2ZwB4tws";

function encode($str){
    $_o=strrev($str);
    // echo $_o;
        
    for($_0=0;$_0<strlen($_o);$_0++){
       
        $_c=substr($_o,$_0,1);
        $__=ord($_c)+1;
        $_c=chr($__);
        $_=$_.$_c;   
    } 
    return str_rot13(strrev(base64_encode($_)));
}

highlight_file(__FILE__);
/*
   逆向加密算法，解密$miwen就是flag
*/
?>
```

分析得该题目就是有一个简单的加密算法和一些php函数组成。

# 0x00 php相关函数

## strrev — 反转字符串

### 说明

**strrev** ( string `$string` ) : string

返回 `string` 反转后的字符串。

### 参数

- `string`

  待反转的原始字符串。

### 返回值

返回反转后的字符串。

### 范例

```php
<?php
echo strrev("Hello world!"); // 输出 "!dlrow olleH"
?>
```

##  str_rot13

str_rot13 — 对字符串执行 ROT13 转换

### 说明

**str_rot13** ( string `$str` ) : string

对 `str` 参数执行 ROT13 编码并将结果字符串返回。

ROT13 编码简单地使用字母表中后面第 13 个字母替换当前字母，同时忽略非字母表中的字符。编码和解码都使用相同的函数，传递一个编码过的字符串作为参数，将得到原始字符串。

### 参数

- `str`

  输入字符串。

### 返回值

返回给定字符串的 ROT13 版本。

### 范例

```php
<?php

echo str_rot13('PHP 4.3.0'); // CUC 4.3.0

?>
```

##  base64_encode

base64_encode — 使用 MIME base64 对数据进行编码

### 说明

**base64_encode** ( string `$data` ) : string

使用 base64 对 `data` 进行编码。

设计此种编码是为了使二进制数据可以通过非纯 8-bit 的传输层传输，例如电子邮件的主体。

Base64-encoded 数据要比原始数据多占用 33% 左右的空间。

### 参数

- `data`

  要编码的数据。

### 返回值

编码后的字符串数据， 或者在失败时返回 **`FALSE`**。

### 范例

```php
<?php
$str = 'This is an encoded string';
echo base64_encode($str);
?>
```

以上例程会输出：

```
VGhpcyBpcyBhbiBlbmNvZGVkIHN0cmluZw==
```

# 0x01 分析

知晓涉及到的每个函数之后，就需要分析for循环做了些什么然后反向执行进行解密。

附上代码：

```php+HTML
<!DOCTYPE html>
<html>
<head>
	<title></title>
</head>
<body>
<?php
$miwen="a1zLbgQsCESEIqRLwuQAyMwLyq2L5VwBxqGA3RQAyumZ0tmMvSGM2ZwB4tws";
$min = str_rot13($miwen);
$min = strrev($min);
$min = base64_decode($min);

for($_0=0;$_0<strlen($min);$_0++){
	$_c=substr($min, $_0, 1);
	$__=ord($_c)-1;
	$_c=chr($__);
	$_=$_.$_c;
}
echo strrev($_);
?>
</body>
</html>
```

# 0x02 mac环境php配置

mac自带apache和php环境，只需要打开即可。

```shell
 sudo apachectl start			# 开启apache服务
 sudo apachectl restart		# 重启apache服务
 sudo apachectl stop			# 停止apache服务
```

apache配置文件路径：/private/etc/apache2/httpd.conf。通过字符串寻找php位置，取消php模块前的注释。

![image-20210322225223394](https://superj.oss-cn-beijing.aliyuncs.com/image-20210322225223394.png)

apache网站服务根目录如下，可直接使用该目录，或自定义。

![image-20210322225249970](https://superj.oss-cn-beijing.aliyuncs.com/image-20210322225249970.png)

