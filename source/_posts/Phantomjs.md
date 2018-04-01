---
title: Phantomjs
date: 2018-01-04 11:49:30
tags: phantomjs
categories:
keywords:
description: 使用 Phantomjs 过程中遇到的问题
---


# 无法打开https网站
用 `phantomjs` 自动登陆并爬取一些数据，发现爬取 `https` 类型的网站的时候无法正常操作

`Phantomjs`中有个`service_args`参数可以忽略https错误

```python
driver = webdriver.PhantomJS(desired_capabilities=cap, service_args=['--ignore-ssl-errors=true'])
```

# 获取 cookie

```python
from selenium import webdriver
import pickle
driver=webdriver.PhantomJS()
driver.get(url)  #此处url填写需要访问的地址
# 获得 cookie信息
cookie_list = driver.get_cookies()
print (cookie_list)
cookie_dict = {}
for cookie in cookie_list:
    #写入文件
    f = open('cookie.txt','wb+')
    pickle.dump(cookie, f)
    f.close()
　　　
cookie_dict[cookie['name']] = cookie['value']
```

# 引用
- <http://www.cnblogs.com/fly-kaka/p/6656196.html>
- <https://www.cnblogs.com/Jacck/p/7675284.html>

