#事件机制

事件驱动模式是Node.js实现高并发关键点之一。

Node.js的事件机制由```EventEmitter```模块实现，Node中很多可以发送事件的核心模块都继承自该模块。