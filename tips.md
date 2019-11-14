## 问题列表

#### 1. 如何阻止冒泡

对 React 熟悉的读者可能知道：

  * React 中的事件机制并不是原生的那一套，事件没有绑定在原生 DOM 上 ，大多数事件绑定在 document 上（除了少数不会冒泡到 document 的事件，如 video 等)
  * 同时，触发的事件也是对原生事件的包装，并不是原生 event
  * 出于性能因素考虑，合成事件（syntheticEvent）是被池化的。这意味着合成事件对象将会被重用，在调用事件回调之后所有属性将会被废弃。这样做可以大大节省内存，而不会频繁的创建和销毁事件对象。

 React 的合成事件还给使用原生事件留了一个口子，**通过合成事件上的 nativeEvent 属性，我们还是可以访问原生事件**。

原生事件上的`stopImmediatePropagation`方法：除了能做到像 `stopPropagation`
一样阻止事件向父级冒泡之外，也能阻止当前元素剩余的、同类型事件的执行（第一个 click 触发时，调用 `e.stopImmediatePropagtion`阻止当前元素第二个 click 事件的触发）。因此这一段代码：

```javascript
componentDidMount() {
  document.addEventListener('click', () => {
    console.log('document click')
  })
}

handleClick = e => {
  console.log('div click')
  e.nativeEvent.stopImmediatePropagation()
}

render() {
  return (
    <div onClick={this.handleClick}>
      click
    </div>
  )
}
```

只会打印出 div click。

#### 2.this.setState 全是异步执行吗？

**实际上， React 控制的事件处理过程，setState 不会同步更新 this.state。而在 React 控制之外的情况， setState会同步更新 this.state。**

什么是 React 控制内外呢？举个例子：

```javascript
onClick() {
  this.setState({
    count: this.state.count + 1
  })
}

componentDidMount() {
  document.querySelectorAll('#btn-raw')
    .addEventListener('click', this.onClick)
}

render() {
  return (
    <React.Fragment>
      <button id="btn-raw">
        click out React
      </button>

      <button onClick={this.onClick}>
        click in React
      </button>
    </React.Fragment>
  )
}
```

id 为 btn-raw 的 button 上绑定的事件，是在 componentDidMount 方法中通过 addEventListener
完成的，这是脱离于 React 事件之外的，因此它是同步更新的。反之，代码中第二个 button 所绑定的事件处理函数对应的 setState
是异步更新的。

#### 3. 为什么不能直接在 JSX 中使用 if...else，只能借用函数逻辑实现呢？

实际上，我们都知道 JSX 会被编译为`React.createElement`。直白来说，`React.createElement` 的底层逻辑是无法运行 JavaScript代码的，而它只能渲染一个结果。

因此 JSX 中除了 JS 表达式，**不能直接写 JavaScript 语法**。准确来讲，JSX 只是函数调用和表达式的语法糖。

#### 4. 状态是放在State还是React Redux里面呢？

首先，数据持久度上，不同状态数据在持久度上大体可以分为三类：

  * 快速变更型
  * 中等持续型
  * 长远稳定型

**快速变更型，**
这类数据在应用中代表了某些原子级别的信息，且显著特点是变更频率最快。比如一个文本输入框数据值，可能随着用户输入在短时间内持续发生变化。这类数据显然更适合维护在
React 组件之内。

**中等持续型数据，** 在用户浏览或使用应用时，这类数据往往会在页面刷新前保持稳定。比如从异步请求接口通过 Ajax
方式得来的数据；又或者用户在个人中心页，编辑信息提交的数据。这类数据较为通用，也许会被不同组件所需求。在 Redux store 中维护，并通过
connect 方法进行连接，是不错的选择。

**长远稳定型数据，** 指在页面多次刷新或者多次访问期间都保持不变的数据。因为 Redux store
会在每次页面挂载后都重新生成一份，因此这种类型的数据显然应该存储在 Redux 以外其他地方，比如服务端数据库或者 local storage。

下面，我们从另一维度：

数据消费范围来分析。数据特性体现在消费层面，即有多少组件需要使用。我们以此来区分 React 和 Redux 的不同分工。
**广义上，越多组件需要消费同一种数据，那么这种数据维护在 Redux store当中就越合理；反之，如果某种数据隔离于其他数据，只服务于应用中某单一部分，那么由 React 维护更加合理。**

具体来看，共享的数据在 React 当中，应该存在于高层组件，由此组件进行一层层传递。如果在 props
传递深度上，只需要一两个层级就能满足消费数据的组件需求，这样的跨度是可以接受的；反之，如果跨越层级很多，那么关联到的所有中间层级组件都需要进行接力赛式的传递，这样显然会增加很多乏味的传递代码，也破坏了中间组件的复用性。这个时候，使用Redux 维护共享状态，合理设置容器组件，通过 connect 来打通数据，就是一种更好的方式。

一些完全不存在父子关系的组件，如果需要共享数据，比如前面提到过的一个页面需要多处展示用户头像。这往往会造成数据辐射分散的问题，对于 React模式的状态管理十分不利。在这种场景下，使用 Redux 同样是更好的选择。

最后一点，如果你的应用有跟踪状态的功能，比如需要完成“重放”，“返回”或者“Redo/Undo”类似需求，那么 Redux 无疑是最佳选择。因为 Redux天生擅长于此：每一个 action 都描述了数据状态的改变和更新，数据的集中管理非常方便进行记录。

#### 5. 扁平化数据状态

扁平化的数据结构是一个很有意义的概念，它不仅能够合理引导开发逻辑，同时也是性能优化的一种体现。请看这样的数据结构：

    {
      articles: [{
        comments: [{
          authors: [{
          }]
        }]
      }],
      ...
    }


不难想象这是一个文章列表加文章评论互动的场景，其对应于三个组件：Article、Comment 和 Author。

相关 reducer 的处理很棘手，如果 articles[2].comments[4].authors1
发生了变化，想要返回更新后的状态，并保证不可变性，操作起来不是那么简单的，我们需要对深层对象结构进行拷贝或递归。

因此，更好的数据结构设计一定是扁平化的，我们对 articles、comments、authors 进行扁平化处理。例如 comments 数组不再存储
authors 数据，而是记录 userId，需要时在 users 数组中进行提取即可：

    {
      articles: [{
        ...
      }],
      comments: [{
        articleId: ..,
        userId: ...,
        ...
      }],
      users: [{
        ...
      }]
    }

不同组件只需要关心不同的数据片段，比如 Comment 组件只关心 comments 数组；Author 组件只关心 users数组。这样不仅操作更合理，而且有效减少了渲染压力。