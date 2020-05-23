---
title: elf文件的GOT和PLT
date: 2020-05-06 12:03:02
id: 20200506120302
tags: 
	- 逆向
	- elf
categories: 网络安全
description: elf文件中的GOT表和PLT表的基础知识。
---

# 0x00 写在开始

首先，我是从PE文件开始学习的，之后接触到了Linux下的ELF文件，在本质上来说，无论是Windows下的PE文件，还是Linux下的elf文件，他们本质上都是一个可执行文件，所运行的平台不同，它们的文件格式也有不同，但究其本质，还是会有互通的地方。

# 0x01 基础知识

首先，elf文件也是由众多的节构成的，可以通过`objdump -h`查看。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200506153826.png)

下面解释`.got`、`.plt`、`.got.plt`三个节。

#### .got

GOT(Global Offset Table)全局偏移表。这是链接器为外部符号填充的实际偏移表。

.got Section中存放外部全局变量的GOT表，例如stdin/stdout/stderr，非延迟绑定

#### .plt

PLT(Procedure Linkage Table)程序链接表。作用是一个跳板，保存了某个符号在重定位表中的偏移量(用来第一次查找某个符号)和对应的.got.plt的对应的地址。它有两个功能，要么在`.got.plt`节中拿到地址，并跳转。要么当`.got.plt`没有所需地址的时候，触发链接器去找到所需的地址。

#### .got.plt

这个是GOT专门为PLT准备的节。保存了重定位地址。`.got.plt`中的值是GOT的一部分。它包含上述PLT表所需地址(已经找到的和需要去触发的)。

.got.plt Section中存放外部函数的GOT表，例如printf，采用延迟绑定

#### 实例

比如printf是一个重定位符号，需要链接该符号时过程是这样：

main函数call  .plt段中的一个地址，这里的第一句话就是跳转到.got.plt中的保存的printf的地址，如果是第一次，那么保存的地址就是.plt中的下一句话，这个下一句话就是压入这个符号在.rel.plt中的重定位表的偏移量，然后ld程序就会根据重定位表中的信息加上这个偏移量找到这个地址，保存到重定位表所指向的地址中，这个地址其实就是.got.plt段的一个地址。

第二次调用时就可以直接获取到.got.plt中保存的地址了。

# 0x02 实践

接下来我们来实践一下，加深对这几个节的认识。

首先要有一个分析的程序，我们用一个helloworld。

```c
//gcc -m32 -no-pie -g -o helloworld_li helloworld.c
//-g 产生有调试符号的程序
#include<stdio.h>
#include<stdlib.h>

int main(int argc, char const *argv[])
{
    puts("Hello World!\n");
    return 0;
}

```

用gdb打开该文件，开始分析：

- 首先反汇编main

  ![](https://superj.oss-cn-beijing.aliyuncs.com/image-20200506162420073.png)

- 找到call puts的地址，用`b *0x804844b`下断点，`r`执行到断点处并通过`si`单步步入。

  ![](https://superj.oss-cn-beijing.aliyuncs.com/20200506164301.png)

  我们可以看到当前指令是jmp跳转指令，跳转到0x804a00c。

  我们之前通过`objdump`查看该文件的各个节，发现0x804a00c是在`.got.plt`中。

- 我们使用`x/wx 0x804a00c`查看这个位置的值。

  ![image-20200506172031681](https://superj.oss-cn-beijing.aliyuncs.com/image-20200506172031681.png)

  发现该地址存着的信息是当前执行指令的下一个位置。所以执行`jmp 0x804a00c`后会到`0x80482e6`的位置。

  这里就可以理解，在第一次执行时，plt在`.got.plt`中没找到puts函数的地址，然后触发链接器去寻找puts函数的地址。

- 通过`finish`执行完当前函数，然后再查看`0x804a00c`位置的内容。

  ![image-20200506173453829](https://superj.oss-cn-beijing.aliyuncs.com/image-20200506173453829.png)

  可以发现该位置的值已经变了，该地值便是puts函数的地址。

# 0x03 总结

主要是在学习rop的时候，中间提到了`return to libc`，通过调用系统函数，而不是shellcode来实现打开shell。有一种方法是`return to PLT`，因为之前学习的是windows下的，对这个PLT很陌生，因此查资料学习了一下。windows下和linux下，有很多共通的地方，比如说这里的`.plt`和`.got.plt`同windows下的PE文件输入表中的`INT`和`IAT`很像。

参考链接：https://www.jianshu.com/p/5092d6d5caa3

