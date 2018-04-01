---
title: 设置django admin 显示为中文
date: 2018-01-02 17:54:22
tags: django
categories:
keywords:
description: 默认的 django admin 组件 显示为英文，设置 django admin 显示为中文很简单
---

默认的 django admin 组件 显示为英文，设置 django admin 显示为中文很简单，只需在 settings.py 的 MIDDLEWARE_CLASSES 中添加一句 `'django.middleware.locale.LocaleMiddleware'` 即可。

如:

```
MIDDLEWARE_CLASSES = (
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'django.middleware.locale.LocaleMiddleware',
)
```


**注意：**

1. `'django.middleware.locale.LocaleMiddleware'` 必须放在 `'django.contrib.sessions.middleware.SessionMiddleware'` 之后。

2. 如果添加上面这句话后还是显示英文，则可能是浏览器语言设置问题，在浏览器语言设置中添加中文并放到首位试试。比如 Firefox 浏览器设置为：Firefox->Edit->Preferences->  Content->Languages 

# 引用
- https://my.oschina.net/means/blog/287753

