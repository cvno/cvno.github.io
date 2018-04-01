---
title: 基于 GV 的 Python api
date: 2018-01-15 13:25:59
tags:
categories: Project
keywords:
description: 用 Google Voice 来做短信验证码。布局海外业务的廉价验证码方案。
---

使用 Python3 来操作 Google Voice 的 API。 

>Git: [code here](https://github.com/cvno/GV-Python-API)

# 2018-02-01
- 修复重写某些方法时的 bug

# 2018-01-15 代码发布

功能列表:

- 发送 SMS
- 拨打电话
- 取消拨打电话
- 标记为已读或未读
- 下载语音留言
- 后台自动检测是否有新信息
- 根据 SMS 的设置自动回复


它也可能是一个廉价的短信验证码方案，目前国内至少有两家大型的互联网公司使用这种方案。

**依赖模块:**

- `pip install selenium`
- `pip install requests`
- `pip install BeautifulSoup`
- `PhantomJS` 一款无界面模拟浏览器, 不同的操作系统安装方法有差异

