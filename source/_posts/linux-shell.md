---
title: linux bash
date: 2017-05-21 00:38:20
tags:
categories: linux
keywords:
description:
---

<!-- more -->

`man builtin` 查看内置命令
`source` 执行 sh 文件
`unset` 卸载函数
`alias` 别名
`unalias` 卸载别名
`hash` 缓存命令路径
`touch {1..10}.txt`  建立 10 个文件

```sh
function test() {
    # 交互，输入的字符赋值给 name
    read -p 'please input your hostname' name
    # 设置主机名为 name 的值，shell 中 $name 来使用变量
    hostnamectl set-hostname $name;
    # 查看主机名
    hostname;
}
```

# 元字符

## \`\<cmd>` 取命令的执行结果

```sh
ls
# anaconda-post.log bin dev etc home lib lib64 media mnt opt proc Python-3.6.1
res=`ls`    # 把 ls 命令的结果赋值给 res
echo $res    # 显示 res 这个变量的值
# anaconda-post.log bin dev etc home lib lib64 media mnt opt proc Python-3.6.1
```

## $() 同 `` 弥补缺憾

```sh
res=`echo `ls`` #嵌套使用后无法达到预想的效果：取echo 一堆文件名的效果。
echo $res # ls
res=$(echo $(ls)) #替代方案
echo $res
# anaconda-post.log bin dev etc home lib lib64 media mnt opt proc Python-3.6.1
```

## ~ ，加目录

```sh
cd ~
pwd # /root
```

## [ ] 字符通配，匹配括号内之一

```sh
ls [abc].txt # a.txt B.txt c.txt
ls [abc][a].txt # aa.txt
ls [a-z].txt  # A.txt ... z.txt
ls [a-Z][0-9][a-x] # a1C.txt
```

## { } 范围
>括号内的开头和结尾必须是空格

```sh
touch {1..10}.txt
# 10.txt  1.txt  2.txt  3.txt  4.txt  5.txt  6.txt  7.txt  8.txt  9.txt
touch {a..z}.txt
#a.txt  c.txt  e.txt  g.txt  i.txt  k.txt  m.txt  o.txt  q.txt  s.txt  u.txt  w.txt  y.txt
#b.txt  d.txt  f.txt  h.txt  j.txt  l.txt  n.txt  p.txt  r.txt  t.txt  v.txt  x.txt  z.txt
```

## ！ 非，取反

```sh
ls [!0-9].txt
a.txt  b.txt  c.txt  d.txt  e.txt  f.txt
```


## $?  上一条码命令是否执行成功

```sh
ls
echo $? # 0 执行成功
sss
echo $? # 127
```

## $ 

```sh
$ # 变量
echo $res
echo $[1+10]    # 11
echo $[10%3]    # 1  取模
```

- & 后台运行

- && 与运算


# 固话命令的方式

```sh
# 执行顺序
/etc/profile    # 1 环境变量
/etc/profile.d/<可执行文件>  # 2
/root/.bash_profile     # 3  当前用户家目录下
/root/.bashrc    # 4
/root/bashrc    # 5
/etc/bashrc     # 6
```


# 正则表达式

## grep

`-n` 显示行号
`-o` 只显示匹配的内容
`-i` 忽略大小写
`-q` 静默
`-v` 取反，其它行
`-w` 单词
`-l` 过滤成功显示文件路径
`-A` 显示匹配成功后的几行 <要加参数 n>
`-B` 显示匹配成功前的几行 <要加参数 n>
`-C` 显示匹配成功的附近几行 <要加参数 n>
`-c` 显示匹配成功的数目

```sh
grep 'root' /etc/passwd
#root:x:0:0:root:/root:/bin/bash
#operator:x:11:0:operator:/root:/sbin/nologin
grep -i 'hello' a.txt
# 正则
grep 'bash$' /etc/passwd # bash 结尾
grep '^root' /etc/passwd # root 开头
grep '^b.n' /etc/passwd # b 开头 中间任意一个字符 n 结尾的
egrep -e 'he' -e 'ha' a.txt # egrep 'h(e|a)' a.txt
grep -r 'root' /test # 查找包含 root 内容文件及内容显示出来
grep -r 'root' /test # 查找包含 root 文件的文件路径
```

*注：最好用 `egrep`*



## 参考
-  http://www.cnblogs.com/OldJack/p/6607155.html
-  http://www.cnblogs.com/linhaifeng/p/6592572.html

# sed
流式编辑器

`-n` 静默模式，不输出
`-e` 指定多个规则，同`grep`
`-i` 对文件直接进行修改

文件形式规则`pattern.sed`记录规则

```sh
# 核心
sed '' test # 没有修改
sed -n '' test  # 静默模式，不输出
sed -f pattern.sed test # 文件形式
sed '3d' test # 删除第 3 行
sed '1,3d' test # 删除 1 2 3 行
sed '1d;3d;' test # 删除 1 行和 3行
sed -n '3p' test # 打印第三行
sed '3c 111' test # 修改第三行为 111
sed '3a 222' test # 追加一行 222
sed '3i 333' test # 在第 3 行前面插入 333
# 正则
sed '/^root/d' /etc/passwd # 删除以 root 开头的行
sed '/ai$/d' test # 删除以 ai 结尾的行
sed '/ai/d' test # 删除有 ai 的行
sed '/ai.*$/d' test   # 同上
sed '/^s/c 1111' test # 把 s 开头的行改为 1111
# 常用
sed 's/root/sb/' test # 匹配所有的 root 改为 sb；如果一行有多个，只改一个
sed 's/root/sb/g' test # 匹配所有的 root 改为 sb；更改多个
sed '/blex/s/sb/SB/g' test # 把blex 这一行的 sb 改为 SB
# 所有 sed 都使用 sed -r
# 把 blex 这一行的 sb 改为 SB  
sed -r '/^[0-9][a-Z]xsb$/s/sb/SB/g' text
sed -r '/^[0-9]([a-Z]+)xsb$/s/sb/SB/g' text # 效果同上
sed -r '/^[0-9]([a-Z]{3})xsb$/s/sb/SB/g' text # 效果同上
# 正则 （）部分概念   \1\2 正则匹配多个部分概念
sed -r 's/^([a-Z]+)([^a-Z])/\2/g' /etc/passwd  # 匹配第一个单词并去掉
sed -r 's/([^a-Z])([a-Z]+)$/\1/g' /etc/passwd # 匹配最后一个单词
sed -r 's/^([a-Z])([^a-Z]+)([a-Z])([^a-Z]+)/\3\2\1\4/g'  # 第一个单词和第二个单词换位置
```

*注意：*

- 正则分多个部分
- 不一定非得 /// 只要是无意义的三个（如：###）

# awk
`-F` 执行分隔符，默认为空格或多个空格
`NF` 分成几段内容
`$NF` 取最后一段内容
`NR` 行号

```sh
awk -F: '{print $1}' /etc/passwd    # 取第一个部分
# $0 是取所有
awk -F: '{print $1,NF}' /etc/passwd
# root 7
awk -F: '{print $NF}' /etc/passwd # 取最后一段内容
awk -F: 'NR==1{print $1,NR}' /etc/passwd    # 行定位 只显示第一行
# root 1
awk -F: 'NR<=3{print $1,NR}' /etc/passwd    # 只要前 3 行
awk -F: 'NR<=3{print $1,"---",NR}' /etc/passwd  # 小段内容之间的分隔
# and
awk -F: 'NR>=3 && NR<=5{print $1,"---",NR}' /etc/passwd # 取几行到几行之间的
# or
awk -F: 'NR<=2 || NR>=7{print $1,"---",NR}' /etc/passwd

# 正则定址
awk -F: '/nologin$/{print $1}' /etc/passwd  # 获取所有不能登录的用户名
awk -F: '$1~/^r.*t$/{print $3}' /etc/passwd # 某一段内容来匹配正则
awk -F: '$1=="root"/^r.*t$/{print $3}' /etc/passwd # 第一段内容匹配 root
awk -F: '$3>=7{print NR,$1}' /etc/passwd # uid 大于 7 的用户名
# 通过变量类传递
count=7
awk -v x=$count -F: '$3>=x{print NR,$1}' /etc/passwd # 活 的代码
```

- 示例

```sh
# 获取网卡地址
ifconfig | awk 'NR==2{print $2}'    # 172.17.0.2
ip=`ifconfig | awk 'NR==2{print $2}'`
echo $ip    # 172.17.0.2
```

# shell 脚本

- 系统变量

`env` 系统环境变量
`set` 所有变量
`PS1` shell 显示的用户

```sh
export money=1000   # 定义全局变量
```

- 变量

```sh
varname = value # 声明变量
echo $varanme   # 显示变量值
unset varname   # 删除变量
money=10
echo $money
echo ${money}0000 # 100000  # 变量边界
测试命令 test []
[ -d /etc ]  # echo $?  查看结果
```

## if

```sh
#!/bin/bash
var='/etc/passwd'
# 接收用户输入
# read -p 'please input you file path:' var
if [ -f $var ]
    then
        echo "$var is regular file"
elif [ -b $var]
    then
        echo "$var is block"
elif [ -d $var ]
    then
        echo "$var is direcotry"
elif [ -h $var ]
    then
        echo "$var is symlink"
else
    echo "$var is unkown"
fi
```

## while

```sh
# 不停的看系统内存状态
while :
do
    free
    sleep 0.5
    clear
done
```


```sh
count=1
while [ $count -le 10]
do
    echo $count
    ((count++))
done
```


```sh
while :
do
    read -p 'username' name
    read -p 'passwd' pwd
    if [ $name = 'hehe' -a $pwd = '123' ]
        then
            echo 'login successful,welcome'
            break
done
echo 'END'
```

## for

```sh
for i in {1..100}
do
    echo $i
done    
```

*？shell 并发线程*

```sh
# 检测可用ip
for i in {1..253}
do
    ping -c1 192.168.1.$i &> /dev/null
    if [ $? -ne 0]
        then
            echo "192.168.1.$i"
    fi
done
```

## case

```sh
ead -p "username: " -t 5 uname
echo
if [[ -z $uname ]]
then
uname=default
fi

case $uname in
root)
echo "welcome $uname"
;;
seker)
echo "welcome $uname"
;;
default)
echo "welcome $uname"
;;
*)
echo "no user $uname"
esac
```

## 函数


```sh
function abc(){
    echo 'aaa';
    echo 'bbb';
}
function start(){
    echo '----'
    res=$[1+2]
    return $res
}
start # 3
```

## 脚本接收参数

```sh
#!/bin/bash
echo $1
echo $2
echo $3
echo $4
echo $5
echo ${10}
```



```sh
#test.sh
echo $0
echo $1
echo $2
echo $3
echo ${11}
echo '$$' $$
echo '$*' $*
echo '$@' $@
echo '$#' $#
echo '$?' $?

'''
测试：python test.sh 1 2 3 4 5 6 7 8 9 10 11
输出结果：
./test.sh
2
11
$$ 14312
$* 1 2 3 4 5 6 7 8 9 10 11
$@ 1 2 3 4 5 6 7 8 9 10 11
$# 11
$? 0
'''
```

- 示例 nginx 进程自动重启

```sh
#!/bin/bash
ps aux | grep nginx | grep -v 'grep'
if [ $? -ne 0]
    then
        systemctl start nginx
fi
```

## 计划任务

```
systemctl status crond
```

## 参考
- http://www.cnblogs.com/linhaifeng/p/6602149.html


