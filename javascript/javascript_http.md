---
title: Javascript HTTP
---

# Javascript HTTP

## XMLHttpRequest

XMLHttpRequest是一套接口，专门用来让Javascript收发HTTP消息的。它是由微软为IE开发的，后来慢慢演变成标准。借助这些工具，在不刷新网页的情况下，Javascript已经在背后和服务器进行好几轮对话了。

* Sending a request

    ```javascript
    var req = new XMLHttpRequest();
    req.open("GET", "example/data.txt", false);
    req.send(null);
    console.log(req.responseText);
    ```

    当然，也可以获得回复的其他信息，比如`status`，`statusText`和头部信息`getReponseHeader("content-type")`。

## Asynchronous Requests

将`open`的最后一个参数设为true，`send`就成了异步的。

当收到服务器回复时，XMLHttpRequest会产生一个`load`事件。

## HTTP sandboxing

Javascript能够访问别的网站，这会产生安全隐患。浏览器会限制Javascript只能访问它自己所来的网站，这称为Same Origin Policy。服务器可以通知浏览器允许别的网站的Javascript访问它。
