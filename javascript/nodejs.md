---
title: Node.js
---

# Node.js

Javascript是没有内建I/O接口的极少数语言之一。所以Nodejs只好用一种特别的基于回调函数的方式来做I/O。

## Asynchronicity

Nodejs使用异步方式来做I/O，大量使用回调函数，这让本来就非线性的程序写起来变得容易，但线性逻辑的程序就很尴尬。不过，基于Javascript，Nodejs的这种方式一般来说还是让代码更加简洁的。

## The node command

node命令和python等解释性语言的解释器用法基本相同。它的交互式环境和浏览器里的console也基本相同。

命令行参数：`process.argv`，这个数组前两个会分别为`node`和程序名。

标准输出：`process.stdout`，这是个输出流。

## Modules

使用`require`加载模块。模块名忽略`.js`后缀。

指定相对路径或绝对路径的参数会在对应路径去寻找。直接写模块名会在内建模块中或者当前目录下的`node_modules`文件夹下去寻找。

和python不同的是，`require`并不是单独写一行就完事的。它有一个返回值，返回一个命名空间（实质是一个函数或object）。在模块文件里，`module.exports`决定了引用这个文件时会返回什么。

* 如果模块名本身就是文件夹的话，这个文件夹里要有一个index.js文件，指定`module.exports`是什么。
* 如果一个文件名去掉`.js`后缀和文件夹名相同的话，带`.js`的文件优先级更高。

`npm install`命令会在当前文件夹下建立node_modules文件夹，并把要安装的模块放进去

## The file system module

* `fs`模块用来处理一些文件系统相关的操作。
* `fs.readFile`从磁盘上读文件，这是一个异步操作，需要传入处理文本内容和错误的回调函数。
* `fs.writeFile`写入磁盘文件。
* `fs.readdir`列举文件夹文件。
* `fs.rename`重命名文件。
* `fs.readFileSync`以同步的方式读取文件。

## The HTTP module

`http`模块可以用来建立一个简单的服务器。

```javascript
var http = require("http");
var server = http.createServer(function(request, response) {
  response.writeHead(200, {"Content-Type": "text/html"});
  response.write("<h1>Hello</h1>"+request.url);
  response.end();
});
server.listen(8000);
```

或一个客户端。

```javascript
var http = require("http");
var request = http.request({
  hostname: "myhost.com",
  path: "/index.html",
  method: "GET",
  headers: {Accept: "text/html"}
}, function(response) {
  console.log("Server responsed with "+response.statusCode);
});
request.end();
```

## Streams

Writable Stream对象支持`write`函数。

`fs.createWriteStream`可以创建一个文件，并返回一个writable流对象。

Readable流可能会产生两种事件，data和end。这个流的`on`方法类似于`addEventListener`，可以传入handler对产生输入和结束输入的事件作出反应。

## Error handling

Nodejs提供一个创建新promise的接口，`Promise.denodeify`。传入一个异步函数，就可以得到一个新的函数。这个新函数和原来的函数用法完全相同，区别是会返回一个promise对象。
