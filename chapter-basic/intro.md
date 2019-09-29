#### 核心概念

React是一个专注于View层解决方案的框架。于Angular，Vue不同，React并不是完整的MVC/MVVM框架，也不是单纯的模板引擎，它的核心思想就是“组件化”，将UI层拆分为一个个组件，然后组合嵌套，最后构建成完成的APP。

* React将涉及DOM 操作的部分剥离开，目的是为了抽象React，同时适用于Web 端和移动端。ReactDOM 的关注点在DOM 上，因此只适用于Web 端。


```
如果想实现完整的MVC架构，还需要Model和Controller。
这时，可以配合Flux 和 Redux 架构一起使用。
```

React的特点如下：
1. 模板单向绑定：数据--->UI更新
2. 用JS创建DOM（建议使用JSX语法），从而可以利用虚拟DOM树创建/更新DOM
3. 提供的API数量很少，使用者必须非常熟悉原生的JavaScript才能够更快上手React开发
4. 函数式编程（函数类似一个管道，进入一个值，会导出另外一个值）

简单来说，**React是一套可以用简洁的语法高效绘制 DOM 的框架**。

```
由于React采用虚拟DOM树，因此，它抽象了DOM绘制，可以将JS DOM Tree翻译为各种平台的UI组件，从而衍生出 `React native`这种跨平台框架。
```

#### 构建React项目

全局安装 `create-react-app`工具，然后利用命令行创建项目

```
npm install -g create-react-app

create-react-app my-app
cd my-app/
npm start
```