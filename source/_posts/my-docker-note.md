---
title: Docker 操作
date: 2018-01-02 10:48:30
tags: docker
categories:
keywords:
description: docker 基本操作
---

# why?
- 更高效的利用系统资源
- 更快速的启动时间
- 一致的运行环境
- 持续交付和部署
- 更轻松的迁移
- 更轻松的维护和扩展

# 基本概念
- 镜像（Image）
- 容器（Container）
- 仓库（Repository）

理解了这三个概念，就理解了 Docker 的整个生命周期。

# 获取镜像

从 Docker 镜像仓库获取镜像的命令是 docker pull。其命令格式为：

```sh
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
```

**如:**

```sh
$ docker pull ubuntu:16.04
16.04: Pulling from library/ubuntu
bf5d46315322: Pull complete
9f13e0ac480c: Pull complete
e8988b5b3097: Pull complete
40af181810e7: Pull complete
e6f7c7e5c03e: Pull complete
Digest: sha256:147913621d9cdea08853f6ba9116c2e27a3ceffecf3b492983ae97c3d643fbbe
Status: Downloaded newer image for ubuntu:16.04
```
## 运行

```
$ docker run -it --rm ubuntu:16.04 bash 
```

说明一下上面用到的参数。

- -it：这是两个参数，一个是 -i：交互式操作，一个是 -t 终端。我们这里打算进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终端。
- --rm：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 --rm 可以避免浪费空间。
- ubuntu:16.04：这是指用 ubuntu:16.04 镜像为基础来启动容器。
- bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash。

进入容器后，我们可以在 Shell 下操作，执行任何所需的命令。这里，我们执行了 cat /etc/os-release，这是 Linux 常用的查看当前系统版本的命令，从返回的结果可以看到容器内是 Ubuntu 16.04.4 LTS 系统。

# 启动容器
启动容器有两种方式，一种是基于镜像新建一个容器并启动，另外一个是将在终止状态（stopped）的容器重新启动。

因为 Docker 的容器实在太轻量级了，很多时候用户都是随时删除和新创建容器。

## 新建并启动
- 无交互

```sh
$ docker run ubuntu:14.04 /bin/echo 'Hello world'
Hello world
```

- 允许交互

```sh
$ docker run -t -i ubuntu:14.04 /bin/bash
root@af8bae53bdd3:/#
```

其中，-t 选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上， -i 则让容器的标准输入保持打开。

**当利用 docker run 来创建容器时，Docker 在后台运行的标准操作包括：**

- 检查本地是否存在指定的镜像，不存在就从公有仓库下载
利用镜像创建并启动一个容器
- 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
- 从地址池配置一个 ip 地址给容器
- 执行用户指定的应用程序
- 执行完毕后容器被终止

## 启动已终止容器

可以利用 docker container start 命令，直接将一个已经终止的容器启动运行。

容器的核心为所执行的应用程序，所需要的资源都是应用程序运行所必需的。除此之外，并没有其它的资源。可以在伪终端中利用 ps 或 top 来查看进程信息。


```sh
root@ba267838cc1b:/# ps
  PID TTY          TIME CMD
    1 ?        00:00:00 bash
   11 ?        00:00:00 ps
```
可见，容器中仅运行了指定的 bash 应用。这种特点使得 Docker 对资源的利用率极高，是货真价实的轻量级虚拟化。

# 后台运行
需要让 Docker 在后台运行而不是直接把执行命令的结果输出在当前宿主机下。可以通过添加 -d 参数来实现。

- 不使用 -d 参数运行容器。

容器会把输出的结果 (STDOUT) 打印到宿主机上面

- 使用了 -d 参数运行容器。

```sh
$ docker run -d ubuntu:17.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
77b2dc01fe0f3f1265df143181e7b9af5e05279a884f4776ee75350ea9d8017a
```

此时容器会在后台运行并不会把输出的结果 (STDOUT) 打印到宿主机上面(输出结果可以用 docker logs 查看)。容器是否会长久运行，是和 docker run 指定的命令有关，和 -d 参数无关。

使用 -d 参数启动后会返回一个唯一的 id，也可以通过 docker container ls 命令来查看容器信息。


```sh
$ docker container ls
CONTAINER ID  IMAGE         COMMAND               CREATED        STATUS       PORTS NAMES
77b2dc01fe0f  ubuntu:17.10  /bin/sh -c 'while tr  2 minutes ago  Up 1 minute        agitated_wright
```
要获取容器的输出信息，可以通过 docker container logs 命令。


```sh
$ docker container logs [container ID or NAMES]
hello world
hello world
hello world
. . .
```


# 终止容器
可以使用 docker container stop 来终止一个运行中的容器。

此外，当 Docker 容器中指定的应用终结时，容器也自动终止。

用户通过 exit 命令或 Ctrl+d 来退出终端时，所创建的容器立刻终止。

终止状态的容器可以用 docker container ls -a 命令看到。例如


```sh
docker ps -a
CONTAINER ID        IMAGE                    COMMAND                CREATED             STATUS                          PORTS               NAMES
ba267838cc1b        ubuntu:14.04             "/bin/bash"            30 minutes ago      Exited (0) About a minute ago                       trusting_newton
98e5efa7d997        training/webapp:latest   "python app.py"        About an hour ago   Exited (0) 34 minutes ago                           backstabbing_pike
```
处于终止状态的容器，可以通过 docker container start 命令来重新启动。

## 重新启动

```sh
$ docker container restart
```
此外，docker container restart 命令会将一个运行态的容器终止，然后再重新启动它。

# 进入容器
在使用 -d 参数时，容器启动后会进入后台。

某些时候需要进入容器进行操作，包括使用 docker attach 命令或 docker exec 命令，推荐大家使用 docker exec 命令，因为使用 attach stdin 执行 exit 会导致容器停止。

## attach 命令
docker attach 是 Docker 自带的命令。下面示例如何使用该命令。


```sh
$ docker run -dit ubuntu
243c32535da7d142fb0e6df616a3c3ada0b8ab417937c853a9e1c251f499f550

$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
243c32535da7        ubuntu:latest       "/bin/bash"         18 seconds ago      Up 17 seconds                           nostalgic_hypatia

$ docker attach 243c
root@243c32535da7:/#
```

## exec 命令
**-i -t 参数**
docker exec 后边可以跟多个参数，这里主要说明 -i -t 参数。

只用 -i 参数时，由于没有分配伪终端，界面没有我们熟悉的 Linux 命令提示符，但命令执行结果仍然可以返回。

当 -i -t 参数一起使用时，则可以看到我们熟悉的 Linux 命令提示符。


```sh
$ docker run -dit ubuntu
69d137adef7a8a689cbcb059e94da5489d3cddd240ff675c640c8d96e84fe1f6

$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
69d137adef7a        ubuntu:latest       "/bin/bash"         18 seconds ago      Up 17 seconds                           zealous_swirles

$ docker exec -i 69d1 bash
ls
bin
boot
dev
...

$ docker exec -it 69d1 bash
root@69d137adef7a:/#
```

# 导出和导入容器
## 导出容器
如果要导出本地某个容器，可以使用 docker export 命令。


```sh
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                    PORTS               NAMES
7691a814370e        ubuntu:14.04        "/bin/bash"         36 hours ago        Exited (0) 21 hours ago                       test
$ docker export 7691a814370e > ubuntu.tar
```

## 导入容器快照
可以使用 docker import 从容器快照文件中再导入为镜像，例如


```sh
$ cat ubuntu.tar | docker import - test/ubuntu:v1.0
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED              VIRTUAL SIZE
test/ubuntu         v1.0                9d37a6082e97        About a minute ago   171.3 MB
```

此外，也可以通过指定 URL 或者某个目录来导入，例如


```sh
$ docker import http://example.com/exampleimage.tgz example/imagerepo
```

*注：用户既可以使用 docker load 来导入镜像存储文件到本地镜像库，也可以使用 docker import 来导入一个容器快照到本地镜像库。这两者的区别在于容器快照文件将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入时可以重新指定标签等元数据信息。*

# 删除
## 删除容器

```sh
$ docker container rm  trusting_newton
trusting_newton
```
如果要删除一个运行中的容器，可以添加 -f 参数。Docker 会发送 SIGKILL 信号给容器。

## 清理所有处于终止状态的容器
用 docker container ls -a 命令可以查看所有已经创建的包括终止状态的容器，如果数量太多要一个个删除可能会很麻烦，用下面的命令可以清理掉所有处于终止状态的容器。


```sh
$ docker container prune
```

# 常用操作

```sh
$ docker --version  # docker版本
$ docker info   # docker信息
$ docker run --rm ubuntu:16.04 /bin/cat '/etc/os-release'
$ docker run -it --name web  --rm ubuntu:16.04 bash
```
## 停止容器


```sh
➜ docker stop myUbuntu
myUbuntu
```
## 删除容器


```sh
➜ docker rm myUbuntu
myUbuntu
```
## 列出镜像

```sh
➜ docker images ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              6b914bbcb89e        3 weeks ago         182 MB
ubuntu              14.04               7c09e61e9035        3 weeks ago         188 MB
```
### 列出部分镜像

```sh
$ docker image ls ubuntu
$ docker image ls ubuntu:16.04
```
docker image ls 还支持强大的过滤器参数 --filter，或者简写 -f

```sh
$ docker image ls -f since=mongo:3.2
$ docker image ls -f 
```
想查看某个位置之前的镜像也可以，只需要把 since 换成 before 即可。

如果定义了 LABEL，还可以通过 LABEL 来过滤

```sh
label=com.example.version=0.1
```
### 以特定格式显示

```sh
$ docker image ls -q
$ docker image ls --format "{{.ID}}: {{.Repository}}"
$ docker image ls --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

## 删除镜像

```sh
$ docker rmi 7c09e61e9035
Untagged: ubuntu:14.04
Untagged: ...
$ docker image rm $(docker image ls -q redis)
```
## 查看容器修改内容


```sh
$ docker diff webserver
C /root
A /root/.bash_history
...
```

## 查看docker进程

```sh
➜  ~ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
## nginx

```sh
$ docker run --name webserver -d -p 80:80 nginx
```

# 引用
- <http://blog.csdn.net/zhengyong15984285623/article/details/66971949>
- <https://yeasy.gitbooks.io/docker_practice/>

