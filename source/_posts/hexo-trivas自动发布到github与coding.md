---
title: hexo+trivas自动发布到github与coding
date: 2020-02-22 21:24:13
id: 20200222212413
tags: 
	- 博客搭建
categories: Hexo
---

# 写在前面

首先，当我们要开始写博客的时候，我们可以有两种选择来发布你写的博客。

其一，在各大博客平台上发布。选择这条途径的话，我们可以省去一系列的麻烦，只专注与自己的内容即可。但是有好多限制，有些内容不能发布。选择的平台可以有很多，如CSDN，简书等。这两个平台都支持markdown的格式，很方便。

其二，则是自己搭建一个博客，可以有动态和静态博客的选择。之前也尝试过动态博客的搭建，首先需要有自己的额服务器，`github`上有着开源的博客框架，大家可以尝试一下，我是太笨，没有成功，各种环境的依赖问题已经炸裂。下面贴上当时尝试的一个`Django`所写的框架地址。

[基于Django的博客系统]: https://github.com/liangliangyy/DjangoBlo

然后则是静态博客的搭建，`Hexo`是一个静态博客的生成框架，使用简单又快速。

下面的文章便介绍我通过`Hexo`搭建博客所爬过的坑。

从开始到结束，按照搭建博客的顺序书写，大家可以按着这个流程搭建，中间遇到问题可以私信我。

# 博客搭建

搭建过程所用到的环境：`Node.js`

##### 第一步，安装`Node.js`。

[Node.js官网]: https://nodejs.org/zh-cn/

安装后在命令行检查安装是否成功。

```shell
node -v
npm -v
```

若无报错并返回版本号，证明安装成功。

##### 第二步，安装Hexo

```shell
npm install -g hexo-cli
```

##### 第三部，初始化Hexo

创建一个文件夹`myblog`，用来存放`Hexo`所生成的文件。

```shell
mkdir myblog
cd myblog
hexo init
```

之后我们在文件夹下可以发现生成了`hexo`博客的文件，目录结构如下。

```
.
├── _config.yml
├── node_modules
├── package-lock.json
├── package.json
├── scaffolds
│   ├── draft.md
│   ├── page.md
│   └── post.md
├── source
│   └── _posts
└── themes
    └── landscape
```

其中我们只需要其中几个目录与文件。

```shell
_config.yml			# 站点配置文件，需要按照自己的信息进行配置
package.json		# 搭建博客过程中所安装的插件都在该文件中配置，一般不用手动修改
scaffolds				# 生成模板，hexo命令生成文件的模板
source					# 生成静态博客的源码文件，_posts下是文章的存放位置
themes					# 博客主题的安装目录，landscape是默认主题，之后安装的主题也都在这个文件夹下
```

现在我们就可以通过以下命令`Hexo`生成博客了。

```shell
hexo clean	# 清理删除public文件夹，每次生成前都需清理
hexo g			# 生成博客，可以发现会生成一个public文件夹
hexo s			# 本地启动hexo server
```

之后就可以通过`http://localhost:4000/`访问博客。

 <img src="https://superj.oss-cn-beijing.aliyuncs.com/20200222225752.png" style="zoom:50%;" />

# 发布到github和coding

目前我们只能在本地访问到我们的博客，现在我们将博客托管到github和coding中。

##### 第一步，注册github和coding的账号

[github]: https://github.com/
[coding]: https://coding.net/

##### 第二步，创建仓库

注册登录后，在首页可发现`new repository`，新建一个仓库。

`github`创建名字为`username.github.io`的仓库，比如我的用户名为`overoptimus`，我的仓库名为`overoptimus.github.io`。

`coding`创建名字为`username`的仓库，比如我的用户名为`overoptimus`，我的仓库名为`overoptimus`。

> 注：coding的仓库名可以为任意

##### 第三步，生成ssh添加到github和coding

在本地命令行中：

```shell
git config --global user.name "yourname"
git config --global user.email "youremail"
```

这里的`yourname`和`youremail`是你在注册时的用户名和邮箱。

可通过以下命令检查是否配置正确。

```shell
git config --list
```

然后生成ssh：

```shell
ssh-keygen -t rsa -C "youremail"
```

这时就会提示你在ssh生成在什么位置，我是在mac环境下，是在`~/.ssh`下。

```shell
.
├── id_rsa
├── id_rsa.pub
```

可发现其中有两个文件，`id_rsa`是秘钥，`id_rsa.pub`是公钥。我们需要将公钥的内容保存在`github`和`codig`中。在网站的setting中可以找到设置ssh的选项。

##### 第四部，安装通过git部署的插件

```shell
npm install hexo-deployer-git --save
```

> 注：以下若无特殊声明，均在myblog目录下。

##### 第四步，修改_config.yml

```yaml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  - type: git
    repo: 
      coding: git@e.coding.net:overoptimus/overoptimus.git
      github: git@github.com:overoptimus/overoptimus.github.io.git
    branch: master
```

将最后的`deploy`修改为上面的样子，注意其中的链接是你的仓库的链接。

##### 第五步，发布到github和coding

```shell
hexo clean
hexo g
hexo s
```

##### 第六步，在github和coding中打开web服务。

分别进入github和coding的仓库中，打开设置页，github是`github pages`，coding是构建与部署中的`静态网站`。

之后就可以通过`https://overoptimus.github.io/`和coding提示的网址访问我们的网站。

##### 第七步，配置个性域名

首先要购买一个域名，可以在阿里云购买，也可以在`GoDaddy`中购买。

然后在解析中，添加`CNAME`类型的解析指向github和coding的网址。

<img src="https://superj.oss-cn-beijing.aliyuncs.com/20200222235720.png" style="zoom:50%;" />

再添加一条记录为`www`的记录。

然后我们再添加两条记录，路线选择境外，记录还是`www`和`@`，记录值为我们的github pages的网址，我的即为`overoptimus.github.io`。

之后我们可以通过自己的域名访问我们的博客。

# 配置主题

从搭建博客到现在也更换过了好几个主题，有`next`、`pure`、`butterfly`。

现在使用的是`butterfly`，配置情况可以参考下面的网址，很详细，按着配置下来就可以了。

[Butterfly](https://jerryc.me/posts/21cfbf15/#快速開始)

# trivas更新源码自动发布博客

