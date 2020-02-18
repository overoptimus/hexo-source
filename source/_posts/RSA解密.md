---
title: RSA解密
date: 2019-12-21 11:33:23
id: 20191221113323
categories: 网络安全
tags:
	- CTF
	- Crypto
---

<!-- more -->

# RSA解密

`openssl rsa -pubin -text -modulus -in warmup pub.pem`

可以解出e和N。

http://factordb.com/通过该网站，输入N，得到p和q。

```python
# coding = utf-8
def computeD(fn, e):
    (x, y, r) = extendedGCD(fn, e)
    # y maybe < 0, so convert it
    if y < 0:
        return fn + y
    return y


def extendedGCD(a, b):
    # a*xi + b*yi = ri
    if b == 0:
        return (1, 0, a)
    # a*x1 + b*y1 = a
    x1 = 1
    y1 = 0
    # a*x2 + b*y2 = b
    x2 = 0
    y2 = 1
    while b != 0:
        q = a / b
        #ri = r(i-2) % r(i-1)
        r = a % b
        a = b
        b = r
        #xi = x(i-2) - q*x(i-1)
        x = x1 - q * x2
        x1 = x2
        x2 = x
        #yi = y(i-2) - q*y(i-1)
        y = y1 - q * y2
        y1 = y2
        y2 = y
    return(x1, y1, a)


p = 275127860351348928173285174381581152299
q = 319576316814478949870590164193048041239
e = 65537

n = p * q
fn = (p - 1) * (q - 1)

d = computeD(fn, e)
print d

```

通过上面的脚本可以得到d。

```python

# coding=utf-8
import math
import sys
from Crypto import PublicKey
arsa = PublicKey.RSA.generate(1024)
arsa.p = 275127860351348928173285174381581152299
arsa.q = 319576316814478949870590164193048041239
arsa.e = 65537
arsa.n = arsa.p * arsa.q
Fn = long((arsa.p - 1) * (arsa.q - 1))
i = 1
while(True):
    x = (Fn * i) + 1
    if(x % arsa.e == 0):
        arsa.d = x / arsa.e
        break
    i = i + 1

with open('private.pem', 'w') as private:
    private.write(arsa.exportKey())

```

在kali中，通过上面的脚本可以生成private.pem。

输入`openssl`进入工作空间，输入`rsautl -decrypt -in flag.enc -inkey private.pem`，可解出明文。

