#### React 同构

![](/assets/iso1.png)

同构，就是一套React代码在服务器上运行一遍，到达浏览器又运行一遍。服务端渲染完成页面结构，浏览器端渲染完成事件绑定。

那如何进行浏览器端的事件绑定呢？

唯一的方式就是让浏览器去拉取JS文件执行，让JS代码来控制。

![](/assets/iso2.png)

更详细的步骤如下：

![](/assets/iso3.png)

#### 第一步：在服务端构建初始 store

扩充 Koa 的路由文件：

```
// server-side ./routes/index.jsx
import Router from 'koa-router';
import { renderToString } from 'react-dom/server';
import { StaticRouter } from 'react-router-dom';
import { Provider } from 'react-redux';
import { createStore } from 'redux'
const router = Router();
router.get('*', async (ctx) => {
    // 在服务端初始化 store 的数据
    const store = createStore(state => state, {
        name: 'Pspgbhu',
        site: 'http://pspgbhu.me',
});
const context = {};
const content = (
<StaticRouter location={ctx.url} context={context}>
    {/* 增加 Provider */}
    <Provider store={store}>
        <App/>
    </Provider>
</StaticRouter>
);
// 获取 store 数据对象
const preloadedState = store.getState();
await ctx.render('index', {
    html: content,
    state: preloadedState, // 将 store 数据传递给 ejs 模板引擎
    });
});
export default router;
```

#### 第二步：模板引擎将初始的 store 渲染到页面中

模板引擎将 koa router 传来的 store 数据赋值给 window.__INITIAL_STATE_ 对象下。

```
<!-- index.ejs -->
<!DOCTYPE html><html>
<head>
<title>React Isomorphic</title>
<link rel='stylesheet' href='/css/style.css' />
</head>
<body>
<div id="app"><%- html %></div>
<script>
    // 将服务端的 store 对象赋值给该变量
    window.__INITIAL_STATE_ = <%- state %>;
</script>
<script src="/js/app.js"></script>
</body></html>
```

#### 第三步：客户端获取 Redux store 的初始值

```
// client-side index.jsx
import React from 'react';
import { hydrate } from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import { createStore } from 'redux';
import { Provider } from 'react-redux';
import App from '../common/App';
// 通过服务端注入的全局变量得到初始的 state
const preloadedState = window.__INITIAL_STATE_;
const store = createStore(state => state, preloadedState);
hydrate(
<Provider store={store}>
    <BrowserRouter>
        <App></App>
    </BrowserRouter>
</Provider>,
document.querySelector('#app'),
);
```

* 优点：
  * 减少首次渲染时间
  * SEO 
* 缺点：
  * 使用 SSR 这种技术，将使原本简单的 React 项目变得非常复杂，项目的可维护性会降低，代码问题的追溯也会变得困难。