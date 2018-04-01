---
title: node:hello world!
date: 2018-03-31 11:43:13
tags:
categories: node
keywords:
description:
---


**nvm**：`nodeJs`版本管理工具，管理`nodejs`版本和`npm`版本
**nodeJs**：`reactNative`开发过程中所需要的代码库。
**npm**：是随同`nodeJs`一起安装的包管理工具，`npm`管理对应`nodeJs`的第三方插件

# 依赖关系

**nvm、nodejs、npm的关系：**

<!-- more -->

1. nvm管理构建`nodejs`和对应的`npm`，`npm`管理对应`nodejs`的第三方插件
2. 一个reactNative项目只包含一个`nodejs`和`npm`,`npm`和`nodejs`是配套关系，一对一的关系。

![nvm构建reactNavite的关系图](https://i.imgur.com/1PMyqE7.jpg)

To install or update nvm, you can use the [install script][2] using cURL:


# 安装

```sh
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```

or Wget:

```sh
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```

<sub>The script clones the nvm repository to `~/.nvm` and adds the source line to your profile (`~/.bash_profile`, `~/.zshrc`, `~/.profile`, or `~/.bashrc`).</sub>

```sh
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

```sh
# 安装 node 指定版本
nvm i 8.11.1
# 切换指定版本
nvm use 8.11.1
# 查看版本
npm -v
node -v
```

# 更换源


```sh
# 临时使用淘宝源
npm --registry https://registry.npm.taobao.org install node-red-contrib-composer@latest
# 全局配置切换到淘宝源
npm config set registry https://registry.npm.taobao.org
# 全局配置切回到官方源
npm config set registry http://www.npmjs.org
# 检测是否切换到了淘宝源
npm info underscore
# 查看源
npm config get registry
```

# Simple App

[阮一峰教程](https://github.com/ruanyf/jstraining/blob/master/demos/README.md#simple-app)

```sh
cnpm i -g webpack
cnpm i -g webpack-dev-server
```



# 参考
- [nvm、nodejs、npm的关系](https://www.cnblogs.com/qqpw/p/6597295.html)
- [nvm 安装（仓库地址）](https://github.com/creationix/nvm/blob/master/README.md)
- [阮一峰 node （git）](https://github.com/ruanyf/jstraining/blob/master/docs/node.md)
- [JavaScript 全栈工程师培训教程](http://www.ruanyifeng.com/blog/2016/11/javascript.html)
- [切换NPM源(淘宝源)](https://blog.csdn.net/qq_27818541/article/details/62886790?locationNum=4&fps=1)


