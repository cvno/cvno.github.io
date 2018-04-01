---
title: 'Charles: Response 出现乱码'
date: 2017-9-18 21:19:42
tags: charles
---
使用 Charles 抓 https 包的时候, Reponse 出现乱码
<!-- more -->

系统环境: MAC
软件版本: 4.2.1
浏览器: Google Chrome

# 原因
没有信任 Charles 的证书,信任 Charles 的证书就好了

# 解决方法

## 安装 Charles 证书
`Help` > `SSL Proxying` > `Install Charles Root Certificate`

![安装 Charles 证书](https://i.imgur.com/JavIfh7.jpg)

## 信任 Charles 证书
安装完成后会自动弹出钥匙串,找到 Charles 的证书信任即可

![信任 Charles 证书1](https://i.imgur.com/gATUIEf.jpg)

![信任 Charles 证书2](https://i.imgur.com/4xU3IVQ.jpg)



## Charles 设置
`Proxy` > `SSL Proxying Settings`

![Charles 设置](https://i.imgur.com/BqoK9Hy.jpg)


## ok


# 后记
发布这篇文章的时候问题已经被解决,但是没有问题截图,有时间的话再补上.


