---
title: linux 目录和文件
date: 2017-05-10 17:21:39
tags:
categories: linux
keywords:
description:
---

linux 系统中，一切皆文件

winsows 中目录结构是`多根`模式 `C:` `D:` `E:`
而 linux 中 `/` 代表的就是`根`

---

<!-- more -->

# linux 目录

![](https://i.imgur.com/27572It.jpg)

参考：http://www.runoob.com/linux/linux-system-contents.html



`media` `mnt` 挂载目录


## 目录

```sh
cd . # 切换目录
# 增（创建目录）
mkdir /a
mkdir /a/b
mkdir -p /b/c   # 递归创建目录
# 查
ls -d /a/e  # 查看目录
ls  # 查看当前文件夹所有内容（不包含隐藏文件）
# 改
mv /a/f /a/l
# 删
rm -r /a/d  # 递归着删下面的东西（包含提示）
rm -rf /a/b # 递归删（没有提示）尽量避免使用
```

# 文件

```sh
# 增（创建文件）
touch a.txt
# 改
mv a.txt b.txt
# 查
cat b.txt # 查看文件内容
# 删
rm b.txt    # -rf 无提示

less /tmp/aaa.txt   # 翻页查看
more /tmp/aaa.txt   # 也是翻页

head /tmp/aaa.txt   # 默认查看前 10 行内容
head -n 1 /aaa.txt # 指定查看的行数

tail -n 1 /a.txt    # 查看最后 1 行内容
tail -f /a.txt    # 动态查看最后添加的内容;如果是用编辑器打开的文件，那么保存后，这个命令看到的是这个文件的所有内容，打开文件这个操作是把文件所有的内容加载到内存，保存的时候是覆盖写进去。

echo asdf > /a.txt  # 覆盖到文件末尾
echo asdf >> /a.txt   # 追加文件内容


```

## 分页查看

```sh
ls --help | less # 分页查看 前面命令的结果
```


