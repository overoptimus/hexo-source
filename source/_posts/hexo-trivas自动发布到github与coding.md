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

这里我贴出我的站点`_config.yml`。

```yml
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# hexo-neat
neat_enable: true
neat_html:
  enable: true
  exclude:  
neat_css:
  enable: true
  exclude:
    - '*.min.css'
neat_js:
  enable: true
  mangle: true
  output:
  compress:
  exclude:
    - '*.min.js'

# Site
title: 0pt1mus
subtitle: 不温不火，不急不躁，了解hows背后的whys
description: 文化水平不够可以读，为人处世不同可以学，钱没有可以赚，唯独你的内心必须坚定，你要不断努力，并且相信你自己绝对是一个有价值，值得被尊重和喜欢的人。
author: 0pt1mus
email: 1040570917@qq.com
language: zh-CN
timezone:
avatar: /images/avatar.jpg
# search: 59fe6eea70113d77622d1c85f2aeb87a

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://superj.site/
root: /
permalink: :year/:month/:day/:id/
permalink_defaults:

# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: true
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:

# Home page setting
# path: Root path for your blogs index page. (default = '')
# per_page: Posts displayed per page. (0 = disable pagination)
# order_by: Posts order. (Order by date descending by default)
index_generator:
  path: ''
  per_page: 10
  order_by: -date

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page

# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: Butterfly

# Search
search:
  path: search.xml
  field: post
  format: html
  limit: 10000

jsonContent:
  dateFormat: DD/MM/YYYY
  posts:
    title: true
    date: true
    path: true
    text: true
    raw: false
    content: false
    slug: false
    updated: false
    comments: false
    link: false
    permalink: false
    excerpt: false
    categories: false
    tags: false
    author: false


feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:

sitemap:
  path: sitemap.xml

baidusitemap:
  path: baidusitemap.xml

# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
 # - type: git
 #   repository: git@github.com:overoptimus/overoptimus.github.io.git
 #   branch: master
  - type: git
    repo: 
      coding: git@e.coding.net:overoptimus/overoptimus.git
      github: git@github.com:overoptimus/overoptimus.github.io.git
    branch: master
```

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

# 开始写博客

到现在我们可以开始写博客了。

```shell
hexo new "文章名"
```

在博客的目录下，也就是myblog下，输入上述命名，可以在`source/_post`下生成`文章名.md`的文件，然后我们编辑该文件，书写文章就可以，`markdown`的语法网上有很多教程，百度一下学习吧。

每次写完之后，进行以下命令：

```shell
hexo clean
hexo g
hexo d
```

这样就可以将你的博客发布上去了。

# 配置trivas实现自动部署博客到github和coding

我们在写博客的过程中，每次写了一篇文章后，就要执行重复的命令去将生成博客，然后推送到`github`和`coding`，并且我们也会需要将源码进行一个备份，如果我们备份在硬盘里，每次写完文章都需要去更新硬盘中的文件，会比较麻烦。下面我介绍通过`trivas`同时实现博客的备份和自动化部署。

首先我们在github中创建一个名为`hexo-source`的仓库。然后在本地执行以下命令。

```shell
git init
git add .
git commit -m "first commit"
git remote add origin https://github.com/overoptimus/hexo-source.git //这里要修改为你自己的仓库地址
git push -u origin master
```

将本地的`hexo`源码推送到远端的仓库。

然后打开`trivas`官网。

[trivas]: https://www.travis-ci.org/

通过github的账户进行登录，然后开启`hexo-source`的`services integration`服务。

![](https://superj.oss-cn-beijing.aliyuncs.com/20200223153544.png)

点击setting添加`Environment Variables`，`name`可以自己命名，`value`添加`github`和`coding`生成的访问令牌，生成的位置`github`在settings->developer settings->personal access tokens，`coding`在个人设置->访问令牌。权限选择选择完整的仓库读写。

然后在本地的博客目录，即`myblog`下，创建`.trivas.yml`文件，内容如下。

```yml
language: node_js # 设置语言

node_js: stable # 设置相应版本

cache:
    apt: true
    directories:
        - node_modules # 设置缓存，传说会在构建的时候快一些

git:
    depth: 1
    submodules: true

before_install:
    - export TZ='Asia/Shanghai'
    - npm install hexo-cli -g

install:
    - npm install # 安装hexo及插件

script:
    - hexo clean # 清除
    - hexo g # 生成

after_script:
    # - git clone https://${GH_REF} pub_web # 因为我有两个仓库，先将发布服务的仓库clone下来，
    # - cp -rf public/* pub_web/ # 将源博客仓库(blog.git)目录下的public文件夹下的文件复制到发布服务的仓库(chenzhijun.github.com.git)中
    # - cd pub_web # 进入到git仓库
    - cd ./public
    - git init
    - git config user.name "overoptimus"
    - git config user.email "1040570917@qq.com"
    - git add .
    - git commit -am "Travis CI Auto Builder :$(date '+%Y-%m-%d %H:%M:%S')" # 零时区，+8小时
    - git push --force --quiet "https://${GITHUB_TOKEN}@${GH_REF}" master:master
    - git push --force --quiet "https://EBlvrRYUzD:${CD_TOKEN}@${CD_REF}" master:master
branches:
    only:
        - master #只监测master分支,这是我自己的博客，所以就用的master分支了。
env:
    global:
        - GH_REF: github.com/overoptimus/overoptimus.github.io.git #设置GH_REF，注意更改yourname,GITHUB_TOKEN:就是我们在travis-ci仓库中配置的环境变量
        - GITHUB_TOKEN: "${github_token}"
        - CD_REF: e.coding.net/overoptimus/overoptimus.git
        - CD_TOKEN: "${cd_token}"
```

注意其中的一些位置要改成你自己的信息，特别注意在推送到`coding`的地址中`https://`后接的一串字符是在你创建`coding`的访问令牌时的页面中提示你的。且其中${xx_token}是与你在`Environment Variable`中的`name`是一致的。

现在设置已经完成，将本地的更改推送到远程仓库，然后就会在trivas的网站中发现，开始自动部署了。过一会儿你就访问你的博客，发现已经更新了。

然后你就会发现，你可以在任意的地方，即使没有`git`、`node.js`的环境，你在源码仓库进行更改并提交后，`trivas`就可以帮助你将更新后的内容同步到你的博客中。

# 写在后面

现在你就可以很方便、优雅的书写自己的博客文章了，如果你并不喜欢`butterfly`这个主题，你也可以百度一下，寻找你自己的最爱。

希望这篇文章能够对你有所帮助。

