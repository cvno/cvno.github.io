---
title: linux 进程管理
date: 2017-05-20 23:37:36
tags:
categories: linux
keywords:
description:
---

<!-- more -->

```sh
less | ps aux
kill -9 <进程ID>
pkill -9 <firefox>
# 工作号
jobs
kill -9 %1  # 结束某个工作号
```

`pstree` 查看进程树

`pgrep` 只获取 PID

