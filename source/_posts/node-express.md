---
title: node-express
date: 2018-03-31 23:57:03
tags: express
categories: node
keywords:
description:
---

需要用`node`写`api`，狗哥说`express`比较合适。
come on.

<!-- more -->

# hello world


```js
npm install -g express  //安装 express
npm install -g express-generator    //安装 express 命令行工具
express -V  // 版本
```

`npm list express` 查看版本

## 导入

```js
var express    = require('express');    // 导入 express
var router = express.Router();
```

## 路由映射

```js
var router = express.Router();  // 接上文

// 子路由 关系对应 req: 用户请求 res:响应用户
router.get('/', function(req, res) {
  res.send('<h1>Hello World</h1>'); // 回应内容
});

app.use('/home', router);   // app 对应的路径
```

## 端口

```js
var port = process.env.PORT || 8080;    // 服务端口

app.listen(port);   // 监听端口
console.log('Magic happens on port ' + port);   // 输出文字
```

## 启动

```sh
node app1.js
# 指定端口启动 bash
# Linux & Mac
PORT=7070 node app1.js

# windows
set PORT=7070
node app1.js
```


# 进阶

## 访问路径

```js
// :name 获取路径的参数
router.get('/:name', function(req, res) {
  // 获取访问的路由 如果加了 / 并且后面有字符就识别不出来
  res.send('<h1>Hello ' + req.params.name + '</h1>');
  // 获取路由上的参数 /?name=gou
  // res.send('<h1>Hello ' + req.query.name + '</h1>');
});
```

## 更多请求方式

```
var bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({ extended: true }));
```
`body-parser`模块的作用，是对`POST`、`PUT`、`DELETE`等 HTTP 方法的数据体进行解析。`app.use`用来将这个模块加载到当前应用。有了这两句，就可以处理`POST`、`PUT`、`DELETE`等请求了。

```js
// post 映射
router.post('/', function (req, res) {
  var name = req.body.name; // 获取用户发来的 name 字段
  res.json({message: 'Hello ' + name}); // 序列化返回给用户
  // 如果字段不正确将返回 undefined
});
```

## 中间件

```js
// 定义方式 next 下一个中间件
router.use(function(req, res, next) {
  console.log('There is a requesting.');    // 业务逻辑
  next();
});
```

# REST API

REST API 的基本用法

（1） 命令行进入`demos/rest-api-demo`目录，执行下面的命令。

```bash
$ npm install -S json-server
```

（2） 在项目根目录下，新建一个 JSON 文件`db.json`。

```javascript
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

（3） 打开`package.json`，在`scripts`字段添加一行。

```javascript
"scripts": {
  "server": "json-server db.json",
  "test": "..."
},
```

（4） 命令行下执行下面的命令，启动服务。

```bash
$ npm run server
```

（5）打开 Chrome 浏览器的 Postman 应用。依次向`http://127.0.0.1:3000/posts`、`http://127.0.0.1:3000/posts/1`发出`GET`请求，查看结果。

（6）向`http://127.0.0.1:3000/comments`发出`POST`请求。注意，数据体`Body`要选择`x-www-form-urlencoded`编码，然后依次添加下面两个字段。

```javascript
body: "hello world"
postId: 1
```

发出该请求后，再向`http://127.0.0.1:3000/comments`发出`GET`请求，查看结果。

（7） 向`http://127.0.0.1:3000/comments/2`发出`PUT`请求，数据体`Body`要选择`x-www-form-urlencoded`编码，然后添加下面的字段。

```javascript
body: "hello react"
```

发出该请求后，再向`http://127.0.0.1:3000/comments`发出`GET`请求，查看结果。

（8）向`http://127.0.0.1:3000/comments/2`发出`delete`请求。

发出该请求后，再向`http://127.0.0.1:3000/comments`发出`GET`请求，查看结果。


# 参考
- https://github.com/ruanyf/jstraining/blob/master/demos/README.md#rest-api
- https://github.com/ruanyf/jstraining/blob/master/demos/README.md#express

