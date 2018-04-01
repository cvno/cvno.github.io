---
title: linx 基础命令
date: 2017-05-15 17:01:20
tags:
categories: linux
keywords:
description:
---

<!-- more -->

# 基础命令
linux 命令分为三部分：`命令本身` `选项` `参数`

```sh
lsof -i:8000  # 查看端口占用
who
whoami
date
cal
clear
su
passwd
man # 帮助
passwd --help # 查看帮助
ls -dl /tmp # 查看目录本身的详细信息 
!cat   # 执行此命令上一次的执行
echo 123 > /tmp/a.txt  # 覆盖写
echo 123 >> /tmp/b.txt # 追加写
df
firefox &  # 后台运行程序
echo /etc/passwd &> /dev/null # 定向到黑洞文件
cat a.txt | sort | uniq # 去重
cat a.txt | sort | uniq -c # 查看去了多少重
cat /etc/passwd | cut -d: -f1,3 # 指定分隔符 ：，输出第 1，3 部分
du -sh /boot/   # 62M	/root 查看文件夹大小
# find
find / -type f  # 查找普通文件
find / -name '*.txt' # 查找以 txt 后缀的文件
find / -size +30M  # 找大小大于 30M 的文件
find / -size -30M  # 找大小小于 30M 的文件
find / -size +10M -size -30M # 大于 10M 小于 30M 的文件
find / -size +2M -type f -name \*.txt
# 创建一个指定类型的文件
dd if=/dev/zero of=/a.txt bs=20M count=1
echo -e "asdf\nwerwer"  # 解析换行符
```

# tar（打包）
在 linux 系统中，文件的后缀名没有任何特殊意义

```sh
touch a.txt b.txt c.txt
# 打包 c->新建 f->文件名 v->打包过程
tar cvf text.tar a.txt b.txt c.txt
# 查看 tar 文件内容 -t
# f-> 查看哪一个文件
tar -tf test.tar
# 解包 x->解 f->需要解包的文件 v->解包过程
# 默认为当前目录
tar xvf test.tar
# 解压缩 -C（大写）指定解压目录
tar xvf test.tar -C /bak    # 解压到指定目录

# ------ 压缩 gzip（效率） -------
gzip a.txt # 压缩
gunzip a.txt.gz # 解压缩
# 打包并压缩
tar cvzf test1.tar.gz a.txt b.txt c.txt
# 解压缩包
tar xvf test1.tar.gz -C /test1

# ------ 压缩 bzip2（质量，压缩率高） -------
bzip2 a.txt
# 打包并压缩
tar cvjf test2.tar.bz2 a.txt b.txt c.txt
# 解压缩包
tar xvf test1.tar.gz -C /test2
```

## 打包文件夹
不带目录名，只打包下面的文件

```sh
# 需要先切换到需要打包的文件夹下面
# *->当前目录下面的所有文件
tar czf /tmp/etc_bak.tar.gz *   # 打包
tar xf etc_bak.tar.gz -C /etc_bak
```

硬盘满的话有两种情况

- 一个文件太大占满了
- 都是小文件，太小了（一个萝卜一个坑）

# `--stdin`

``` sh
echo 123 | passwd --stdin root
# --stdin 是将 echo 显示的结果作为下一个命令的输入，root 这里指的是修改哪一个用户的密码
# 回车后直接把用户名密码改掉了
```

# vi
vim（vi modiy） 是 vi 的增强版本

`a` 编辑模式（append模式）：当前位置后移一位开始编辑
`i` 插入模式（insert模式）：在当前位置插入
`o` 新建行插入
`O` 基于上一行开始写（当前位置的后面整体后移）
`$` 当前行的结尾
`0` 当前行头部
`dd` 删除行  n dd
`D`  删除空行
`dd p` 剪切
`P` 当前行的上一行
`yy p` 复制到当前行的下一行 n yy
`u` 撤销操作
`control + r` 恢复操作
`G` 文件的最后一行
`n G` 跳到指定行
`gg` 文件的首行
`H` 当前屏幕的头部
`M` 当前屏幕中间
`L` 当前屏幕的最后一行

# 硬盘

- fdisk (小于2T的硬盘)
- parted (大于2T)

```sh
df -h
ls /dev/sda
fdisk /dev/sdb # 分区 sdb 硬盘设备
# 格式化磁盘
mkfs.ext3 /dev/sdb1 # 格式化主分区为ext3系统
mkfs.ext4 /dev/sdb5 && mkfs.ext4 /dev/sdb6 # mkfs.ext4 /dev/sdb5 && mkfs.ext4 /dev/sdb6

mount /dev/sdb1 /sdb1   # 挂载分区
```

# ifconfig

网卡配置

```sh
ifconfig
...
ifconfig <网卡> 192.168.16.48/24  # 修改 ip
# 修改 DNS
vim /etc/resolv.conf    # 这个文件
route add default gw 192.168.1.1 netmask 255.255.255.0 # 添加默认网管
route -m # 查看网关
route del default gw 192.168.1.1 netmask 255.255.255.0 # 删除默认网管

# ---- 永久修改 ----
cd /etc/stsconfig/network-sctipts
vim <网卡名>

TYPE=Ethernet            //协议
BOOTPROTO=static          //dhcp（动态获取）    静态ip
IPADDR=192.168.16.57            //    IP地址
NETMASK=255.255.255.0            //子网掩码
GATEWAY=192.168.16.254            //网关
DNS1=192.168.16.254            //DNS
DEFROUTE=yes            //
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s5
UUID=151b7ea3-6585-49b4-8442-e25a9acfbeb6
DEVICE=enp0s5            //设备名称
ONBOOT=yes            //开机自启动
```

# ssh

```sh

ssh 192.168.1.123 -p 8888 # 指定端口

# 上传
scp /etc/hosts 192.168.1.123:/root
# 下载
scp 192.168.1.123:/root/aaa.txt /tmp/
```

# 免密码

```sh
ssh-keygen  # 一路回车
ls /root/.ssh/
# id_rsa   钥匙
# id_rsa.pub  锁  发给别人
# 传送到服务器
ssh-copy-id -i 192.168.1.123    # 传送完成后会自动重命名为 authorized_keys

# 配置文件
vim /etc/ssh/sshd-config
systemctl restart sshd

netstat -an | grep 8888 # 查看端口

ssh-copy-id -i id_rsa.pub 192.168.1.123 
```
# 计算器

```sh
yum install bc -y
echo "scale=3;10/3"|bc -l
```

