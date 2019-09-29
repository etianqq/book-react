# Node特点

####1.Node.js采用事件驱动、异步编程，为网络服务而设计

从Node.js提供的支持模块，许多函数都是异步执行（比如文件操作等等）。

为了方便服务器开发，Node.js的网络模块特别多，包括HTTP、DNS、NET、UDP、HTTPS、TLS等，开发人员可以在此基础上快速构建Web服务器。以简单的helloworld.js为例：

```
var http = require('http');
http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('Hello World\n');
}).listen(80, "127.0.0.1");
```
一个简单的http服务器，在本地监听80端口，对于任意的http请求，服务器都返回一个头部状态码为200、Content-Type'值为text/plain'的”Hello World“文字响应。

####2.单进程、单线程模式运行

既然Node.js采用单进程、单线程模式，那么在如今多核硬件流行的环境中，单核性能出色的Node.js如何利用多核CPU呢？

解决方案：**运行多个Node.js进程，利用某些通信机制来协调各项任务**。目前，已经有不少第三方的Node.js多进程支持模块发布。
