---
title: Windwos二进制木马技术（学习笔记）
date: 2019-10-04 20:55:01
id: 20191004225501
categories: 网络安全
tags: 
	- 系统安全
	- 二进制 
---

<!-- more -->

# Windows二进制木马技术

## 1. 静态木马的注入流程

首先将目标的exe文件放在项目中，运行注入程序，将目标exe路径输入注入程序的运行窗口，在release下生成了tag.exe,将生成的tar.exe拷贝进原来程序的位置。
运行起服务器，执行tar.exe，从服务器下载下来东西之后，继续进行原来的操作。

## 2. 地址的重要性

每一个pe文件加载进内存中，都独立分配了4g的内存空间，这是Windows的虚拟内存管理机制决定的。

### 简单的栈溢出

```c
#include<stdio.h>
#include<stdlib.h>

void Msg()
{
  MessageBoxA(NULL, NULL, NULL, 0);
}

void Add(int a, int b)
{
  return a + b;
}

void main()
{
  printf("%d", Add(1, 2));
  system("pause");
 	return; 
}
```

上面是正常的代码，可以正常执行，但是如果将`Add`函数改成下面的样子：

```c
void Add(int a, int b)
{
  int *p = &a;
  p--;
  *p = (int)Msg;
  return a + b;
}
```

这个时候再执行代码，将会执行`Msg`函数，弹出一个信息框。 

原因是p指针指向`a`所在的位置，`a`在内存中的栈中，当`p--`，p指针指向了栈中的返回地址，再将返回地址改为`Msg`的地址，当函数返回时，就执行到了Msg。

## 3. PE文件概述

## 4. PE文件加载内存

内存中的一个节若不占到1000字节，将会进行填充，填充成1000个字节。这样做的目的是进行内存对齐，这样是为了读取内存更快，更好的管理内存，以达到Windows需要的速度。

exe文件的加载基址是0x00400000. 

在磁盘中，文件对齐是以200个字节。 

PE文件中有基地址和入口点，基地址与入口点之和就是程序的入口。

## 5. FS寄存器

FS寄存器中保存着Windows程序运行环境的位置TEP。TEP中可找到PEB。

## 6. TEB、PEB

在PE加载到内存中时，PEB中存在结构体，其中存储着很多系统API的地址，可以在遍历PEB的时候（一层一层的往下找），获取到API的地址，从而调用。 

## 7. 远程代码注入原理

首先，在目标程序中分配两个空间，一个参数空间，一个代码空间。

通过系统API`VirtualAllocAx`函数进行内存的分配，需要调用 `OpenProcess` 函数传递个`hProcess` 才能调用成功。之后，通过`WriteProcessMemory` 将代码和参数写入分配的内存中（该函数也需要`OpenProcess` 函数传递参数`hProcess` 才能调用）。 

最后，调用`CreateRemoteThread` 函数，将线程调用起来。

具体调用过程如下：

```
void FuncThread(void *p){
	
}
HANDLE hProcess = openprocess(PROCESS_ALL_ACCESS, FALSE, 11536);
char *pFunc = (char*)VirtualAllocEx(hProcess, 1024*1024, MEN_COMMIT, PAGE_EXECUTE_READWRITE);
WriteProcessMemory(hProcess, (VOID*)pFunc, FuncThread, 1024*1024, NULL);
CreateRemoteThread(hProcess, NULL, 0, (LPTHREAD_START_ROUTINE)FuncThread, (void*)IpParmer, 0, NULL);
```

> 注：函数指针的调用
>
> ```
> char * FUNC(int a, int b);
> typedef char * (_stdcall *FUNC)(int a, int b) Funca;
> ```

## 8. 底层内存原理

- 内存页
- GDT LDT
- 段选择子
- 物理地址-线性地址-逻辑地址

## 9. Windbg常用命令

之后单独用一章来记录学习

## 10. exe捆绑的两种实现

演示：首先打开捆绑软件，在软件中分两次打开两个不同的exe文件，之后选中生成图标的exe，之后点击生成，就生成一个tar.exe文件。

## 11. 文件操作实现

## 12. API实现