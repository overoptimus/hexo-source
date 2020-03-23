---
title: docker的简单使用
date: 2020-03-23 16:42:59
id: 20200323164259
tags: docker使用
categories: docker
---

本文所写环境是在mac下，所以其他环境下的安装过程就不在赘述，如有需要，可以自行百度，资源还是不少的。

在学习docker之前，建议先熟悉linux下的命令操作和相关的背景知识学习。

# Docker概念

docker是一个开源的应用容器引擎。诞生于2013年初，基于Go语言实现，dotCloud公司出品（后改为Docker Inc）。docker可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的linux机器上。

容器是完全使用沙箱机制，相互隔离。容器性能开销极低。

docker从17.3版本之后分为CE（Community Edition社区版）和EE（Enterprise Edition企业版）。

 docker是一种容器技术，解决环境迁移问题。

我们可以将docker简单的理解为是一种虚拟化技术，虚拟出一个个的虚拟机，然后在这一个个虚拟机中部署服务，当迁移的时候，直接迁移整个虚拟机，将环境和代码同时迁移，解决在生产环境中的环境不匹配的问题。

# MAC下Docker安装

mac下安装docker可以通过`homebrew`。`homebrew`的Cask已经支持Docker for Mac。

```shell
brew cask install docker
```

`homebrew`是一个mac下的包管理工具。

## 配置阿里云镜像加速器

因为默认情况下是从国外下载镜像，因此速度格外的慢。好在大厂已经提供了镜像供我们使用。这里以配置阿里云镜像为例。

打开阿里云网站，登录，搜索`容器镜像服务`，打开后如下：

![](https://superj.oss-cn-beijing.aliyuncs.com/截屏2020-03-23下午5.03.38.png)

找到自己的加速器地址。点击状态栏docker->Preferences->Deamon。

![](https://superj.oss-cn-beijing.aliyuncs.com/截屏2020-03-23下午5.09.32.png)

点击`+`，将自己的加速器地址添加到`Registry mirrors`。然后重启Docker服务，完成配置。

# Docker架构

![](https://superj.oss-cn-beijing.aliyuncs.com/20200323171743.png)

image和container的关系就相当于类和对象的关系。

**镜像(image)**：Docker镜像，就相当于是一个root文件系统，比如官方镜像`ubuntu:16.04`就包含了完整的一套`ubuntu16.04`最小系统的root文件系统。

**容器(container)**：镜像（image）和容器（container）的关系，就像是面向对象程序设计中的类和对象一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

**仓库(Repository)**：仓库可以理解为git的仓库，git保存的是代码，Docker保存的是镜像。Docker的远程仓库是`Docker Hub`。

# Docker命令

### 镜像相关命令

| 命令                            | 含义                                   |
| ------------------------------- | -------------------------------------- |
| docker images                   | 查看下载的镜像                         |
| docker search centos            | 搜索镜像，如centos                     |
| docker pull centos[:7]          | 拉取镜像，[]可加可不加，是确定其版本。 |
| docker rmi centos[:7]           | 删除镜像                               |
| docker rmi \`docker images -q\` | 删除所有镜像                           |

### 容器相关命令

| 命令                                             | 含义                                  |
| ------------------------------------------------ | ------------------------------------- |
| docker ps                                        | 查看容器                              |
| docker ps -a                                     | 查看所有容器，包括停止的              |
| docker run -it --name=容器名 mysql /bin/bash     | 创建mysql镜像的容器，并打开交互命令行 |
| docker run -id --name=容器名 mysql:5.0 /bin/bash | 后台创建mysql:5.0镜像的容器           |
| docker exec -it 容器名 /bin/bash                 | 交互命令行打开已创建容器              |
| docker start 容器名                              | 启动容器                              |
| docker stop 容器名                               | 停止容器                              |
| docker rm 容器名/容器id                          | 删除容器                              |
| docker inspect 容器名                            | 查看容器信息                          |

# docker容器的数据卷

1. docker容器删除后，在容器中产生的数据还在吗？

   容器删除后，在容器中产生的数据也会随着容器的删除而被删除。

2. docker容器和外部机器可以直接交换文件吗？

   docker容器是一个封闭的容器，无法和外部机器交换文件。

3. docker容器之间想要进行数据交互？

   因docker容器的封闭，容器之间一般是无法进行数据交换的。

为了解决这三个问题，引入了**数据卷**。

### 概念

数据卷是宿主机中的一个目录或文件。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200323190809.png)

在容器中也创建一个目录，将宿主机中的目录挂载到容器中的这个目录，这个时候宿主机的目录就称为数据卷。

当容器目录和数据卷目录绑定后，对方的修改会立即同步。

一个数据卷可以被多个容器同时挂载。 

**数据卷的作用：**

- 容器数据持久化
- 外部机器和容器间通信
- 容器之间数据交换

### 配置数据卷：

创建容器时，使用-v参数设置数据卷。

```shell
docker run -id --name=c1 -v 宿主机目录(文件):容器内目录(文件) centos /bin/bash
```

**注意事项：**

- 目录必须是绝对路径
- 如果目录不存在，会自动创建
- 可以挂载多个数据卷

### 数据卷容器

在多容器进行数据交换时，为了方便管理，又引入了数据卷容器的概念，数据卷容器实质是一个容器，它实现了挂载一个目录或文件，然后其他容器再挂载这个容器，它便称之为数据卷容器。

概念比较绕，看下图可以更好理解。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200323191537.png)

其中`Data Container c3`便是一个数据卷容器。

**配置：**

1. 首先配置数据卷容器

   ```shell
   docker run -it --name=c3 -v /volume centos /bin/bash
   ```

   使用-v参数挂载一个目录，这里没有指定宿主机目录位置，会在宿主机中创建一个默认的目录。

2. 创建启动c1，c2容器，使用--volumes-from参数，设置数据卷容器。

   ```shell
   docker run -it --name=c1 --volumes-from c3 centos /bin/bash
   docker run -it --name=c2 --volumes-from c3 centos /bin/bash
   ```

# Docker应用部署

## mysql部署

在docker容器中部署mysql，并通过外部mysql客户端操作mysql。

#### 操作步骤

1. 搜索mysql镜像

   ```shell
   docker search mysql
   ```

   搜索mysql，确定要下载的版本。

2. 拉取mysql镜像

   ```shell
   docker pull mysql[:version]
   ```

   将mysql的镜像下载到本地。

3. 创建容器

   首先现在宿主机中创建一个mysql的文件夹，方便管理文件。

   ```shell
   mkdir mysql
   cd mysql
   ```

   创建容器：

   ```shell
   docker run -id -p 3306:3306 --name=m_mysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root mysql
   ```

   参数说明：

| 参数                           | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| -p 3306:3306                   | 将容器的3306端口映射到宿主机的3306端口                       |
| -v $PWD/conf:/etc/mysql/conf.d | 将主机当前目录下的conf/my.cnf挂载到容器的/etc/mysql/my.cnf。配置目录 |
| -v $PWD/logs:/logs             | 将主机当前目录下的logs目录挂载到容器的logs。日志目录         |
| -v $PWD/data:/var/lib/mysql    | 将主机当前目录下的data目录挂载到容器的/var/lib/mysql。数据目录 |
| -e MYSQL_ROOT_PASSWORD=root    | 初始化root用户的密码。                                       |

4. 通过宿主机或者外部机器中的mysql客户端，操作mysql。

## Nginx部署

在docker容器中部署Nginx，并通过外部机器访问Nginx。

#### 操作步骤

1. 搜索Nginx镜像。

2. 拉取Nginx镜像。

3. 创建容器。

   首先创建nginx目录，方便之后的管理。

   创建conf目录，进入创建nginx.conf文件，粘贴下面内容。

   ```
   #user  nobody;
   worker_processes  1;
   				#error_log  logs/error.log;
   #error_log  logs/error.log  notice;
   #error_log  logs/error.log  info;
   				#pid        logs/nginx.pid;
   				events {
       worker_connections  1024;
   }
   				http {
       include       mime.types;
       default_type  application/octet-stream;
   				#log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
       #                  '$status $body_bytes_sent "$http_referer" '
       #                  '"$http_user_agent" "$http_x_forwarded_for"';
   				#access_log  logs/access.log  main;
   				sendfile        on;
       #tcp_nopush     on;
   				#keepalive_timeout  0;
       keepalive_timeout  650;
           client_max_body_size 20m;
           proxy_connect_timeout     300; 
           proxy_read_timeout      300; 
           proxy_send_timeout      300;
   				#gzip  on;
   				server {
           listen       80;
           server_name  localhost;
   				#charset koi8-r;
   				#access_log  logs/host.access.log  main;
   				location / {
               root   html;
           }
   				#error_page  404              /404.html;
   				# redirect server error pages to the static page /50x.html
           #
           error_page   500 502 503 504  /50x.html;
           location = /50x.html {
               root   html;
           }
   				# proxy the PHP scripts to Apache listening on 127.0.0.1:80
           #
           #location ~ \.php$ {
           #    proxy_pass   http://127.0.0.1;
           #}
   				# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
           #
           location ~ \.php$ {
               root           html;
               fastcgi_pass   127.0.0.1:9000;
               fastcgi_index  index.php;
                           fastcgi_read_timeout 300;
               fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name; 
               include        fastcgi_params;
           }
   				# deny access to .htaccess files, if Apache's document root
           # concurs with nginx's one
           #
           #location ~ /\.ht {
           #    deny  all;
           #}
       }
   				# another virtual host using mix of IP-, name-, and port-based configuration
       #
       #server {
       #    listen       8000;
       #    listen       somename:8080;
       #    server_name  somename  alias  another.alias;
   				#    location / {
       #        root   html;
       #        index  index.html index.htm;
       #    }
       #}
   				# HTTPS server
       #
       #server {
       #    listen       443 ssl;
       #    server_name  localhost;
   				#    ssl_certificate      cert.pem;
       #    ssl_certificate_key  cert.key;
   				#    ssl_session_cache    shared:SSL:1m;
       #    ssl_session_timeout  5m;
   				#    ssl_ciphers  HIGH:!aNULL:!MD5;
       #    ssl_prefer_server_ciphers  on;
   				#    location / {
       #        root   html;
       #        index  index.html index.htm;
       #    }
       #}
       include vhosts/*.conf;
   }
   ```

   开启docker容器：

   ```shell
   Docker run -id --name=d_nginx -p 80:80 -v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf -v $PWD/logs:/var/log/nginx -v $PWD/html:/usr/share/nginx/html nginx
   ```

# Dockerfile

1. Docker镜像的本质是什么？

   Docker镜像的本质是分层的文件系统。

2. Docker中一个centos镜像为什么只要200MB，而一个centos操作系统的ISO文件要几个G？

   centos的iso镜像文件包含bootfs和rootfs，而docker的centos镜像不用操作系统的bootfs，只有rootfs和其他镜像层。

3. Docker中一个tomcat镜像为什么有500MB，而一个tomcat安装包只要70多MB？

   由于docker中镜像是分层的，tomcat虽然只有70MB，但他需要依赖于父镜像和基础镜像，所有整个对外暴露的tomcat镜像大小就是500MB。

**操作系统组成部分：**

- 进程调度子系统
- 进程通信子系统
- 内存管理子系统
- 设备管理子系统
- 文件管理子系统
- 网络通信子系统
- 作业控制子系统

#### 文件管理子系统

linux文件系统由bootfs和rootfs两部分组成。

bootfs：包含bootloader（引导加载程序）和kernel（内核）。

rootfs：root文件系统，包含的就是典型linux系统中的/dev，/proc，/bin，/etc等标准目录和文件。

不同的linux发行版，bootfs基本相同，而rootfs不同，如ubuntu、centos等。

## Docker镜像原理

docker镜像是由特殊的文件系统叠加而成。

最底端是bootfs，并使用宿主机的bootfs。

第二层是root文件系统rootfs，称为base image。

然后再往上可以叠加其他的镜像文件。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200323212355.png)

统一文件系统（UNION File System）技术能够将不同的层整合成一个文件系统，为这些层提供了一个统一的视角，这样就隐藏了多层的存在，在用户的角度看来，只存在一个文件系统。

一个镜像可以放在另一个镜像的上面。位于下面的镜像称为父镜像，最底部的镜像称为基础镜像。

当一个镜像启动容器时，docker会在最顶层加载一个读写文件系统作为容器。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200323212437.png)

## 镜像制作

Docker下有两种方法来制作镜像。

### 容器转镜像

```shell
docker commit 容器id 镜像名:版本号
docker save -o 压缩文件名称 镜像文件:版本号
docker load -i 压缩文件名字
```

以上前两条命令可以将docker容器打包成镜像，并保存成压缩文件。第三条命令可以将压缩文件重新载入到docker中，生成镜像。

commit命令在将容器打包成镜像时，若该容器有数据卷，数据卷中的内容不会打包到镜像中。

### Dockerfile文件

dockerfile是一个文本文件，包含了一条条的指令。每一条指令构建一层，基于基础镜像，最终构建出一个新的镜像

对于开发人员：可以为开发团队提供一个完全一致的开发环境

对于测试人员：可以直接拿开发时所构建的镜像或者通过Dockerfile文件构建一个新的镜像开始工作。

对于运维人员：在部署时，可以实现应用的无缝移植。

#### Dockerfile文件中的关键字

| 关键字      | 作用                     | 备注                                                         |
| ----------- | ------------------------ | ------------------------------------------------------------ |
| FROM        | 指定父镜像               | 指定dockerfile基于哪个image构建                              |
| MAINTAINER  | 作者信息                 | 用来标明这个dockerfile谁写的                                 |
| LABEL       | 标签                     | 用来表名dockerfile的标签，可以使用Label代替Maintainer最终都是在docker image基本信息中可以查看 |
| RUN         | 执行命令                 | 执行一段命令，默认是/bin/sh，格式：RUN command或者RUN  ["command","param1","param2"] |
| CMD         | 容器启动命令             | 提供启动容器时候的默认命令和ENTRYPOINT配合使用。格式 CMD command param1 param2或者CMD  ["command","param1","param2"] |
| ENTRYPOINT  | 入口                     | 一般在制作一些执行就关闭的容器中会使用                       |
| COPY        | 复制文件                 | build的时候复制文件到image中                                 |
| ADD         | 添加文件                 | build的时候添加文件到image中，不仅仅局限于当前build上下文，可以来源于远程服务 |
| ENV         | 环境变量                 | 指定build时候的环境变量，可以在启动容器的时候通过-e覆盖，格式ENV  name=value |
| ARG         | 构建参数                 | 构建参数，只在构建的时候使用的参数，如果有ENV，那么ENV的相同名字的值始终覆盖arg的参数 |
| VOLUNE      | 定意外不可以挂载的数据卷 | 指定build的image哪些目录可以启动时候挂载到文件系统中，启动容器的时候使用-v绑定。格式 VOLUME  ["目录"] |
| EXPOSE      | 暴露端口                 | 定义容器运行的时候监听的端口，启动容器的时候使用-p来绑定暴露端口。格式  EXPOSE 8080 或者 EXPOSE 8080/udp |
| WORKDIR     | 工作目录                 | 指定容器内部的工作目录，如果没有创建则自动创建，如果指定/使用的是绝对地址，如果不是/开头，那么实在上一天workdir的路径的相对路径 |
| USER        | 指定执行用户             | 指定build或者启动的时候，用户在RUN CMD ENTRYPOINT执行的时候的用户 |
| HEALTHCHECK | 健康检查                 | 指定监测当前容器的健康监测的命令，基本上没用，因为很多时候，应用本身有健康监测机制。 |
| ONBUILD     | 触发器                   | 当存在ONBUILD关键字的镜像作为基础镜像的时候，当执行FROM完成之后，会执行ONBUILD的命令，但是不影响当前镜像，用处也不怎么搭 |
| STOPSIGNAL  | 发送信号量到宿主机       | 该STOPSIGNAL指令设置将发送到容器的系统调用信号以退出。       |
| SHELL       | 指定执行脚本的shell      | 指定RUN  CMD ENTRYPOINT执行命令时候使用的shell               |

#### dockerfile案例

需求：自定义一个centos7镜像，设置默认工作路径为/usr，安装net-tools、vim。

dockerfile：

```dockerfile
FROM centos:7
MAINTAINER 0pt1mus <xxxxx@xx.com>
RUN yum install -y vim net-tools
WORKDIR /usr
CMD /bin/bash
```

在dockerfile文件所在的目录下执行：

```shell
Docker build -f ./centos_dockerfile -t superj_centos:1 .
```

最后的`.`是告诉docker在build时候的上下文环境是当前目录。

由于 docker 的运行模式是 C/S。我们本机是 C，docker 引擎是 S。实际的构建过程是在 docker 引擎下完成的，所以这个时候无法用到我们本机的文件。这就需要把我们本机的指定目录下的文件一起打包提供给 docker 引擎使用。

如果未说明最后一个参数，那么默认上下文路径就是 Dockerfile 所在的位置。

**注意**：上下文路径下不要放无用的文件，因为会一起打包发送给 docker 引擎，如果文件过多会造成过程缓慢。

# Docker服务编排

服务编排：按照一定的业务规则批量管理容器。

微服务架构的应用系统中一般包含若干个微服务，每个微服务一般都会部署多个实例，如果每个微服务都要手动启停，维护的工作量会很大。

- 要从dockerfile build     image或者去dockerhub拉取image
- 要创建多个container
- 要管理这些container（启动停止删除）

## Docker Compose

`Docker compose`是一个编排多容器分布式部署的工具，提供命令集管理容器化应用的完整开发周期，包括服务构建，启动和停止。

使用步骤：

1. 利用dockerfile定义运行环境镜像
2. 使用docker-compose.yml定义组成应用的各个服务
3. 运行docker-compose up启动应用

在MAC的环境下，用`homebrew`安装Docker后，`docker compose`已默认安装，可直接使用。

### 案例

使用docker compose编排nginx+springboot项目

1. 创建docker-compose目录

   ```shell
   mkdir docker-compose
   cd docker-compose
   ```

2. 编写docker-compose.yml文件

   ```yaml
   version: '3'
   services:
     nginx:
       image: nginx
       ports:
       	- 80:80
       links:
       	- app
       volumes:
       	- ./nginx/conf.d:/etc/nginx/conf.d
     app:
       image: app
       expose:
       	- "8080"
   ```

3. 创建./nginx/conf.d目录

   ```shell
   mkdir -p nginx/conf.d
   ```

4. 在./nginx/conf.d目录下编写nginx.conf文件

   ```nginx
   server{
   	listen 80;
   	access_log off;
   	
   	location / {
   		proxy_pass http://app:8080;
   	}
   }
   ```

5. 在docker-compose目录下使用docker-compose启动容器

   ```shell
   docker-compose up
   ```

6. 测试访问。

