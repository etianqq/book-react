# Node简介

####Node.js不是JS应用、而是JS运行平台

* **Node.js采用C++语言编写而成，是一个Javascript的运行环境。**
* Node.js采用了Google Chrome浏览器的V8引擎，性能很好。
* 提供了很多系统级的API，如文件操作、网络编程等

最简单的运行命令如下：
```
# node helloworld.js
```
备注：
Javascript是一种脚本语言，必须通过解释器来运行。一般，浏览器包含了Javascript引擎，其专门负责解释执行网页中的Javascript代码。

有了Node，Javascript就可以在非浏览器的环境下运行，比如，可以编写系统级或者服务器端的Javascript代码，交给Node.js来解释执行。

#### 电子书

Gitbook: [https://etianqq.github.io/book-node/](https://etianqq.github.io/book-node/)

其他书籍：

* [Javascript简明讲义](https://etianqq.github.io/book-jstips/)
* [HTTP讲义](https://etianqq.github.io/book-http/)
* [VUE2.0讲义](https://etianqq.github.io/book-vue2/)
* [Angular 1.0讲义](https://etianqq.github.io/book-angular/)

#### Debug

* `gitbook build`
* `gitbook serve`
* 打开 [http://localhost:4000](http://localhost:4000)

#### 发布

* `gitbook build`
* `npm run deploy`
