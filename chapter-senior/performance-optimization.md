#### 性能优化

* 在 HTML 内实现 Loading 态或者骨架屏；
* 去掉外联 css；
* 缓存基础框架 - HTTP 缓存资源；
* 使用动态 polyfill；
* 使用 SplitChunksPlugin 拆分公共代码；
* 正确地使用 Webpack 4.0 的 Tree Shaking - 在引入这个模块，却没有使用它时，webpack 会自动把它 Tree Shaking 丢掉；
* 使用动态 import，切分页面代码，减小首屏 JS 体积；
    * React Loadable 是一个专门用于动态 import 的 React 高阶组件，你可以把任何组件改写为支持动态 import 的形式。
* 编译到 ES2015+，提高代码运行效率，减小体积；
* 使用 lazyload 和 placeholder 提升加载体验。


#### 代码上的优化

* 列表项定义key属性
* 组件的属性值尽量不要用内联函数，如<Com1 action={()=>doSomething()}/>
* 适当时使用React.PureComponent和useMemo
* 利用shouldComponentUpdate定义更新条件
