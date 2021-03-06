#### 错误处理

Error boundaries 是 React 组件，它会在其子组件树中的任何位置捕获 JavaScript 错误，并记录这些错误，展示降级 UI 而不是崩溃的组件树。Error boundaries 组件会捕获在渲染期间，在生命周期方法以及其整个树的构造函数中发生的错误。

如果 class 组件定义了生命周期方法 `static getDerivedStateFromError()` 或 `componentDidCatch()` 中的任何一个（或两者），它就成为了 Error boundaries。

1. `static getDerivedStateFromError(error)`: 适合处理降级渲染，在UI上显示错误视图。
2. `componentDidCatch(error, info)`: 适合记录错误日志，比如上报服务端。

例子如下：
```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染可以显示降级 UI
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // "组件堆栈" 例子:
    //   in ComponentThatThrows (created by App)
    //   in ErrorBoundary (created by App)
    //   in div (created by App)
    //   in App
    logComponentStackToMyService(info.componentStack);
  }

  render() {
    if (this.state.hasError) {
      // 你可以渲染任何自定义的降级 UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

如果了解React Fiber，就应该知道，V16版本之后，React生命周期分为两个阶段：

- Render Frase
- Commit Frase

两个阶段的分界岭是`render`函数。
第一阶段生命周期函数可能会被重复调用；而一旦进入第二阶段，代码会一气呵成执行完毕。

如果异常发生在第一阶段，调用`getDerivedStateFromError`，如果异常发生在第二阶段，调用`componentDidCatch`。所以，`getDerivedStateFromError`适合显示UI错误组件，后者由于在`render`之后调用，适合处理非UI操作，如错误上报。

