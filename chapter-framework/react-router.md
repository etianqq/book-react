#### React Router

单页面应用（SPA）可以让Web应用看起来像多页面应用，URL变化时，不会向服务端发起请求，而是利用自身监听路由变化而更新UI。

通过使用React Router可以让Web应用根据不同URL渲染不同组件。

#### 1. 路由器

React Router通过 `Router`和`Route`两个组件完成路由功能。

* Router：路由器。一个应用只需要一个Router。
* Route：路由配置。其为Router的子组件。

路由方式有两种（都是Router的子组件）
* BrowserRouter：使用Html5 的History API（pushState, replaceState等）实现I和URL同步
```
http://example.com/some/path
```
**使用BrowserRouter，需要对服务器进行配置**，让服务器能处理所有可能正确的URL（需要服务器返回SPA应用中唯一的HTML页面）
* HashRouter：使用URL的Hash实现I和URL同步
```
http://example.com/#/some/path
```
**无法服务器配置**

#### 2. 路由配置

* path：用来描述这个Route匹配的URL路径
* match：当URL和Route匹配时，Route会创建一个match对象作为props中的一个属性传递给被渲染的组件。
```
match {
    params, // 参数，如<Route path='/foo/:id'>，那么，当URL为'http://example.com/foo/1'时，params = {id: 1}
    isExact, // boolean值。是否完全匹配
    path, // Route的path属性
    url // URL的匹配部分
}
```

#### 3. Route渲染组件

* 方式一：component
```
<Route path='/foo' component={Foo}>
```
* 方式二：render（可以向组件传递额外属性；每次路由到都会创建新的组件）
```
<Route path='/foo' render={props=><Foo {...props} data={extraData}/>}>
```
* 方式三：children（无论是否匹配成功，children返回的组件都会被渲染）
```
<Route path='/foo' childern={props=>
    <div className={props.match ? 'active': ''}>
        <Foo />
    </div>}>
```

#### 4. switch和exact (React Router 4)

当URL和多个Route匹配时，如果只想让第一个匹配的Route渲染，那么可以将这个Route放在Switch组件中。

如果想让Route和URL完全匹配时，才渲染Route，那么使用exact属性。

```
<Router>
    <Switch>
        <Route exact path='/' component={Home} />
        <Route path='/posts' component={Post} /> 
        <Route path='/:user' component={User}/ >
    </Switch>    
</Router>


```

#### 5. 嵌套路由

在Route渲染的组件内部定义新的Route。比如，改造上面的Post路由组件：

```
const Post = (match=>{
    return (
        <div>
            <Route path={`${match.url}/:id`} component={PostDetail} />
            <Route exact path={match.url} component={PostList} >
        </div>
    )
})
```

#### 6. 链接

* Link组件

```
<Link to={{
    pathname: '/post',
    search: '?sort=name',
    hash: '#hash',
    state: {name: 'React'} // 传递数据
}}>
```
* JS操作

```
this.props.history.push('/post');
this.props.history.replace('/post');
```
