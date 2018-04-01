---
title: linux 用户
date: 2017-05-10 19:54:04
tags:
categories: linux
keywords:
description:
---

linux 下添加一个用户，就相当于公司招聘一个人，linux 每健一个用户，就会以这个用户名新建一个组。

<!-- more -->

# 用户

```sh
id <user> # 查看用户信息
uid=1000(aaa) gid=1000(aaa) groups=1000(aaa)
# 用户 id，组 id（初始组），
# /etc/passwd  用户信息文件
aaa:x:1000:1000::/home/aaa:/bin/bash
# x 用户密码占位符，代表这个用户需要密码登录，如果没有这个 x ，直接就可以登录系统
# 1000 uid
# 1000 gid
# 两个冒号空的那一段是用户描述信息
# /home/aaa   指定用户的家目录
# /bin/bash  用户的登录 shell；可登录的用户
# /sbin/nologin 不可登录的用户
# /etc/shadow  记录用户密码文件
```

```sh
mkdir /test_user
# 添加用户
useradd -u 1200 -g root -d /test_user -c 'test user' -s /bin/bash test_user
# -u 指定用户 uid
# -g 指定主组
# -d 指定用户家目录
# -c 用户描述信息
# -s 指定用户登录 shell
# -G 指定附加组
# 如果新添加的用户指定 root 为默认组，那么在 group 中 root 中就不会有新添加的这个用户

# 修改用户
usermod -G root test_user
```


# 组

```sh
# 添加组
groupadd group1

# 删除组
groupdel group1

# 修改组
groupmod -g 2014 group1 # 修改组 id
groupmod -n group2 group1 # 修改组名

# /etc/gshadow  记录组密码文件
# /etc/group  记录用户组文件
gpasswd aaa # 设置组密码
#编辑文件每一行最后面的字符就是组内的成员，可以通过修改文件来改变用户的组或添加组
--- 修改文件来添加组员
root:x:0:aaa
~ id aaa
uid=1000(aaa) gid=1000(aaa) groups=1000(aaa),0(root)
---
gpasswd -r aaa  # 清除组密码
```

# 加深理解


linux 里面一切皆文件，在创建用户的时候同样是修改文件。所以，也可以直接修改文件来创建用户。

1. `vim /etc/passwd` 追加一行所添加的用户
2. `mkdir /home/rose` 创建家目录
3. `vim /etc/shadow` 追加一行，同样的格式
4. `vim /etc/group` 追加一行，和uid需要一致，名字可以不一样
5. `vim /etc/gshadow` 追加一行
6. `touch /var/spool/rose` 创建用户系统邮箱文件
7. `cp -r /etc/skel/.[!.]* /home/rose` 拷贝指定目录下所有以`.`开头的文件，`-r`递归着拷贝
8. `chown -R rose.rose /home/rose` 修改为自己的权限
9. `chown rose.mail /var/spool/mail/rose` 把邮箱文件权限改为自己的

selinux 这个软件如果开启了，开启后所创建的文件权限后面都会加上`.`

```sh
vim /etc/sysconfig/selinux
# SELINUX = enforcing 改为 disabled  关掉，然后重启才会生效

```

