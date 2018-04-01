---
title: 'hexo: ERROR Process failed: _posts/*'
date: 2017-12-18 14:49:12
tags: hexo
---

文章的格式出现错误了

<!-- more -->

- 错误提示

```sh
$ hexo s
ERROR Process failed: _posts/*
Error at 
.....
```


- 原因

```
title: Hexo
date: 2017-12-13 12:21:33
tags:Hexo #tags冒号后面应当有个空格,其他地方也应当注意
```

- 正确格式(加上那个空格就好了)

```
title: Hexo
date: 2017-12-13 12:21:33
tags: Hexo
```

# 参考资料
- http://shitaibin.github.io/2015/12/13/hexo-errors/

