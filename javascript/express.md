---
title: Nodejs Web Framework: Express
---

# Nodejs Web Framework: Express

## 安装

新建一个工程：新建文件夹，`npm init`，一路回车。

安装express为一个依赖模块：`npm install express --save`。

安装完成后，你会发现`node_modules`文件夹里出现大量的模块，不只是一个express文件夹。因为express依赖很多。

## Hello World

利用express，可以轻松创造一个简单的Web服务器。

下面的代码是做什么的，应该一看就秒懂。

```javascript
const express = require('express');
const app = express();

app.get('/', function(req, res) {
  res.send('Hello World!');
});

app.listen(12345, function() {
  console.log('Listening on port 12345!');
});
```

用浏览器访问本地端口12345，看到Hello World。

## Routing

Routing指的是决定怎样回应一个HTTP+URI请求的过程。

收到一个HTTP+URI请求，首先对HTTP方法和URI中指定的路径进行匹配，匹配成功就交给对应的Handler处理。一个Handler被称为一个Middleware。

Express程序框架可以这样描述：建立一个express实例（上述代码中的`app`），然后给它绑定一堆Route，最后启动它。

上述代码中`app.get`语句就是直接给express实例绑定一个Route的办法。这个Route匹配HTTP方法GET和路径`/`，传给一个发送"Hello World"的Handler。

一个请求可能会被多个Route匹配，这种情况下，可以在一个Route的Middleware中用`next()`函数把这个请求传递下去。

```
app.get('/', function(req, res, next) {
  res.send('Hello World!');
  next();
});
```

~~`next()`函数可以随意传递参数，这些参数会被下一个Route的Middleware放在参数列表的最前面。~~

一个函数可能绑定多个Route，比如`app.all`就会对同一个path建立许多Route，即对每个HTTP方法建立一个。

Route用以匹配的path可以是正则表达式。不过其语法规则中去掉了符号（`.`）和（`-`）的特殊用法，使其只能匹配字面值。

URI中可以指定参数。这些参数会通过`req`传进Middleware中，通过`req.params`访问。

```
path: /users/:userId/books/:bookId
url: http://localhost:12345/users/34/books/8989
req.params: {"userId": "34", "bookId": "8989"}
```

一个Route可以有多个Middleware，直接在函数参数里往后补充就可以了。

### Route的模块化

将Route封装为一个模块，可以大大提高代码的可维护性。

要把Route作为一个模块，需要使用express.Router类。一个Router可以绑定许多Route，最后用一个`use`函数，把它的所有Route绑定到express实例中。

```javascript
// hello.js
var express = require('express');
var router = express.Router();
router.get('/about', function(req, res){
  res.send("Hello world!");
});
module.exports = router;
```

在主程序里，使用这个Router。

```javascript
var hello = require('./hello');
// ...
app.use('/hello', hello);
```

最终匹配这个Router中的Route的路径是两个path的组合，在这个例子里是`/hello/about'。

## 使用Express-Generator

这个网站是不是太寒酸了？一个稍微有点样子的网站，哪怕没有内容，也需要许多基础的配置，比如CSS，favicon，等等。

为了迅速搭建起一个网站框架，Express提供了一个工具，express-generator。

```javascript
$ npm isntall express-generator -g
$ express --view=pug website_name
$ cd website_name
$ npm install
$ PORT=12345 npm start
```

## View Engine

如果所有回复都用`res.send()`来写，那显然会让整个代码变得一团糟。幸好Expressjs支持发送动态生成的网页。该网页由模板填充而来，填充模板的工具被称为View Engine。目前Express-generator产生的网站默认使用的View Engine为Pug。你可以在app.js中看到如下代码：

```javascript
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');
```

这两行把文件夹views定为网页模板所在的文件夹，把pug设为网页生成引擎。要在Route中回复网页，用的是`res`的`render`函数。`render`函数的两个参数第一个是网页模板的路径，第二个是模板中未定参数的赋值，由一个字典来表示。
