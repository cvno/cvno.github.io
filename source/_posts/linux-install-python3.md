---
title: linux 安装 python
date: 2018-05-05 23:13:58
tags: python
categories:
keywords:
description:
---


Python3 的安装方法

```sh
which python
# 可以查看位置，一般是位于/usr/bin/python目录下。
```

<!-- more -->

# Ubuntu 18.04

```sh
# py3
sudo apt install python3
sudo apt-get install python3-pip
# py2
sudo apt install python
sudo apt-get install python-pip
```


# Centos

## 依赖

```sh
yum -y groupinstall "Development tools"
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
```

然后根据自己需求下载不同版本的Python3，我下载的是Python3.6.5

```sh
wget https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tar.xz
```

>如果速度不够快，可以直接去官网下载，利用WinSCP等软件传到服务器上指定位置，我的存放目录是/usr/local/python3，使用命令：

## 创建目录

```sh
mkdir /usr/local/python3 
# 建立一个空文件夹
```

然后解压压缩包，进入该目录，安装Python3

## 编译

```sh
tar -xvJf  Python-3.6.5.tar.xz
cd Python-3.6.5
./configure --prefix=/usr/local/python3
# 安装
make && make install
```

## 创建软链接

```sh
# python3
ln -s /usr/local/python3/bin/python3 /usr/bin/python3
# pip3
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```


## Other

`python3.6`程序的执行文件：`/usr/local/bin/python3.6`
`python3.6`应用程序目录：`/usr/local/lib/python3.6`
`pip3`的执行文件：`/usr/local/bin/pip3.6`
`pyenv3`的执行文件：`/usr/local/bin/pyenv-3.6`

### yum

因为 yum 使用 Python 2，因此替换为 Python 3 后可能无法正常工作，因此修改 yum 配置文件


```sh
sudo vi /usr/bin/yum
# ------------------------------
cd /usr/bin
ls yum*
yum yum-config-manager yum-debug-restore yum-groups-manager
yum-builddep yum-debug-dump yumdownloader
```
更改以上文件头，将第一行指定的 `python` 版本改为 `python2.7`（`#!/usr/bin/python` 改为 `#!/usr/bin/python2.7`）

### gnome

```sh
# 修改gnome-tweak-tool配置文件
vi /usr/bin/gnome-tweak-tool
```
`#!/usr/bin/python` 改为 `#!/usr/bin/python2`

### urlgrabber

```
vi /usr/libexec/urlgrabber-ext-down
```
`#!/usr/bin/python` 改为 `#!/usr/bin/python2`


# pip

```sh
pip install --upgrade pip
pip3 install --upgrade pip
```


**参考资料**

- [WhiteBlackCat ( cnblog )](https://www.cnblogs.com/FZfangzheng/p/7588944.html)
- [| Ehlxr's Blog](https://ehlxr.me/2017/01/07/CentOS-7-%E5%AE%89%E8%A3%85-Python3%E3%80%81pip3/)
- [文成小盆友 ( cnblog )](http://www.cnblogs.com/wenchengxiaopenyou/p/5709218.html)
- [一蓑风雨任平生的专栏 ( CSDN )](https://blog.csdn.net/hobohero/article/details/54381475)

