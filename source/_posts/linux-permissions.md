---
title: linux 权限
date: 2017-05-24 19:41:31
tags:
categories: linux
keywords: 权限
description: 权限是操作系统用来限制资源访问的机制，权限一般分为读、写、执行。
---


系统中每个文件都拥有特定的权限、所属用户及所属组，通过这样的机制来限制哪些用户、哪些组可以对特定的文件进行什么样的操作。

每个进程都是以某个用户的身份运行，所以进程的权限与该用户的权限一样，用户的权限越大，该进程所拥有的权限也就越大。

<!-- more -->

```sh
drwxr-xr-x  22 hyhnm  staff   748B  2 24 19:41 .
drwxr-xr-x   9 hyhnm  staff   306B 12 27 19:22 ..
-rw-r--r--@  1 hyhnm  staff   913B 12 20 21:18 Charles-Response-error.md
-rw-r--r--@  1 hyhnm  staff   895B  2  1 14:59 GV-python-api.md
-rw-r--r--@  1 hyhnm  staff   1.0K  1  4 11:57 Phantomjs.md
```

```sh
ll -dl /test # 查看指定目录权限
ll a.txt # 指定文件权限
```


# 文件标识符

`drwxr-xr-x` `d`代表的是目录
`-rwxr-xr-x` `-`代表的是普通文件
`lrwxr-xr-x` `l`代表的是符号链接（快捷方式）
`crw-rw-rw-` `c`代表的是专门设备文件
`brw-r-----` `b`代表的是块专门设备文件
`srwxr-xr-x` `s`代表的是套接字
`prwxr-xr-x` `p`代表的是命名管道文件


# 权限标识

权限三个一组（rwx），对应 UGO 分别设置（总共有 3 个组 9 个权限）

| 可读 r | 可写 w |  可执行x |
| --- | --- | --- |
| 4 | 2 | 1 |

rwx rwx rwx = 777

| rwx | rwx | rwx |
| --- | --- | --- |
| 属主 U | 属组 G | 其他人 O |

Linux 权限基于 UGO 模型进行控制：U 代表 User，是文件或文件夹所属用户的权限；G 代表 Group，是文件或文件夹所属组的权限；O 代表 Other，是其他用户对文件或文件夹的权限

![](https://i.imgur.com/Mt3Z2rm.jpg)



# 修改权限

命令 `chmod` 用以修改文件的权限：

示例：`chmod 777`

**mode 参数格式如下：**

```
u、g、o     分别代表用户、组、其他
a           代表ugo
+、-        代表加入或删除对应权限
r、w、x     代表三种权限
-R          递归地修改
```

**示例：**

```
chmod u +rw test.md    给文件的所属用户添加rw权限
chmod g -x test.md     给文件的所属组移除x权限
chmod go +r test.md    给文件的所属组和其他用户添加r权限
chmod a -x test.md     给文件的所属UGO三个模型均移除x权限
chmod u=rw,g=-,o=rwx test.md
```

命令 chmod 也支持以三位八进制数值的方式修改权限，rwx 权限值分别由数字表示如下：

```
r = 4 (2 ^ 2)
w = 2 (2 ^ 1)
x = 1 (2 ^ 0)
```
使用数字表示权限时，每组权限分别为对应数字之和：

```
rw  = 4 + 2 = 6
rwx = 4 + 2 + 1 = 7
r-x = 4 + 1 = 5
```
所以，使用数字表示 UGO 权限时，可以用如下方式表示：

```
chmod 0660 test.md  设置 UGO 权限为 rw-rw----
chmod 0775 test.md  设置 UGO 权限为 rwxrwxr-x
```

# 修改文件所属用户和组
命令 chown 用以改变文件的所属用户：

```sh
chown username filename
```
常用参数：

```
-R   归地修改目录下所有文件的所属用户
```
命令 chgrp 用以改变文件的所属组：

```sh
chgrp groupname filename
```
常用参数：

```
-R   递归地修改目录下所有文件的所属组
```


# 实例
如何修改文件的拥有者的权限为可读可写不可执行？

```sh
chmod u=rw <file name>
```

# 总结

- 文件
    - read：查看文件内容
    - write：修改文件内容
    - x：执行文件（把文件当做命令一样执行）
- 目录
    - read：浏览目录下的子目录名，子文件名
    - write：创建、重命名、删除主目录、子文件
    - x：可以 cd 目录（有这个权限才可以修改目录下面的文件）

# 参考
- http://blog.sina.com.cn/s/blog_81aecb810102waaq.html
- http://blog.csdn.net/qq_36221862/article/details/56012469
- https://www.cnblogs.com/mingc/p/7591287.html
- http://blog.csdn.net/fan_zhen_hua/article/details/2050009

