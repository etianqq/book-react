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
    * 不推荐用索引作为key，因为一旦列表中的数据发生重排，数据的索引也会发生变化
    * key只要不在当前列表中重复即可
* 组件的属性值尽量不要用内联函数，如<Com1 action={()=>doSomething()}/>
* 适当时使用React.PureComponent和useMemo
* 利用shouldComponentUpdate定义更新条件
* React官方建议把state当作不可变对象。当组件的状态都是不可变对象时，shouldComponentUpdate只需**浅比较**就可以判断状态是否真的改变，从而避免不必要的render调用。
    * 状态类型是不可变类型 - number, string, boolean, null, undefined
    * 状态类型是array，创建新的数组返回（concat, slice, filter, map 会返回一个新数组）：
       ```
       // add
       this.setState({
           books: [...preState.books, 'New book']
       })
       // remove
       this.setState({
           books: preState.books.slice(1, 3)
       })
       // filter
       this.setState({
           books: preState.books.filter(item=>item !== 'React)
       })
       ```
    * 状态类型是object，创建新的对象返回（Object.assign，对象扩展语法，或者Immutable库）
        ```
        this.setState({
           owner: Object.assgin({}, preState.owner, {name: 'Jason'})
       })

        this.setState({
            owner: {...preState.owner, name: 'Jason'}
        })
        ```
    