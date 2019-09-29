# React是什么？

* React不是一个MVC框架，它是构建易于可重复调用的web组件，侧重于UI, 也就是view层
* 其次React是单向的从数据到视图的渲染，非双向数据绑定
* 不直接操作DOM对象，而是通过虚拟DOM通过diff算法以最小的步骤作用到真实的DOM上。
* 不便于直接操作DOM，大多数时间只是对 virtual DOM 进行编程
