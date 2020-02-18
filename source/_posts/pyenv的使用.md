---
title: pyenv的使用
date: 2020-01-06 21:03:55
id: 20200106210355
categories: python
tags:
	- mac环境
	- python
---

<!-- more -->

# 0x00 mac下python

首先，在mac的`os x`环境下，本身自带的python2的环境。目录在：

```shell
/System/Library/Frameworks/Python.framework/Versions/2.7/bin:${PATH}
```

通常我们一般通过`brew`来安装python。

```shell
brew install python
brew install python3
```

目录分别为：

```shell
/usr/local/Cellar/python/3.7.3/Frameworks/Python.framework/Versions/3.7/bin:${PATH}
/usr/local/Cellar/python@2/2.7.16/Frameworks/Python.framework/Versions/2.7/bin:${PATH}
```

在安装了python3，之后通常我们使用`python`打开python2，使用`python3`来使用python3，用`pip`和`pip3`来进行第三方库的安装，但是这种方式下，对于第三方库的安装和python本身的使用比较麻烦，环境问题会很麻烦。这个时候，我们就可以使用`pyenv`来管理我们的python环境，方便又直观。

# 0x01 pyenv

### 介绍

​	python多版本管理工具，python环境路径直观清晰，管理方便。

### 安装pyenv

​	通过brew进行安装

```shell
brew install pyenv
```

​	在安装之后，会提示将以下信息复制到`~/.zshrc`或`~/.bash_profile`下，具体写入哪个文件，和你当前使用shell种类有关。

```shell
export PATH="~/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

​	执行命令`source ~/.zshrc`或`source ~/.bash_profile`式环境变量生效。

​	安装后，在当前用户的根目录下生成`.pyenv`目录，通过`pyenv`安装的python都在下面的目录下。

```shell
~/.pyenv/versions/
```

### pvenv使用

```shell
# 查看可以安装的python版本
pvenv install -l

# 安装指定版本
pyenv install 3.8.1

# 设置全局python版本
pyenv global 3.8.1

# 设置当前目录版本
pyenv local 3.8.1

# 查看全局python版本
pyenv global

# 查看当前目录python版本
pyenv local

# 查看系统中安装的python版本，并提示当前使用的python版本
pyenv version
```

使用python时，直接在终端输入`python `会打开当前目录所设置的python版本的交互shell。



