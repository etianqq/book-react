#### React Hook

** Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。**

例子：
```
import React, { useState } from 'react';

function Example() {
  // 声明一个新的叫做 “count” 的 state 变量
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

上面的例子里，**useState** 就是一个 Hook。

#### 什么是 Hook?

Hook 是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的函数。**Hook 不能在 class 组件中使用**。

React 内置了一些像 useState 这样的 Hook。你也可以创建你自己的 Hook 来复用不同组件之间的状态逻辑。

内置Hook：

* useState
* useEffect

#### State Hook

`useState` 与 class 里面的 this.state 提供的功能完全相同。一般来说，在函数退出后变量就就会”消失”，而 state 中的变量会被 React 保留（类似JS闭包）。

`useState`的每次调用都有一个完全独立的 state —— 因此你可以在单个组件中多次调用同一个自定义 Hook。如下：

```
// 声明多个 state 变量！
const [age, setAge] = useState(42);
const [fruit, setFruit] = useState('banana');
const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
```

使用步骤：

##### 1.声明 State 变量

```
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 "count" 的 state 变量
  const [count, setCount] = useState(0);
} 
```

##### 2.读取 State

```
 <p>You clicked {count} times</p>
```

##### 3.更新 State
```
<button onClick={() => setCount(count + 1)}>
    Click me
</button>
```
#### Effect Hook

**useEffect** 就是一个 Effect Hook，给函数组件增加了操作副作用的能力。它跟 class 组件中的 componentDidMount、componentDidUpdate 和 componentWillUnmount 具有相同的用途，只不过被合并成了一个 API。

下面的例子实现两个功能：
1. 在title上面显示点击次数
2. 订阅好友在线状态，并在实现取消订阅功能

```
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    // 对应类实现 componentDidMount 和 componentDidUpdate
    document.title = `You clicked ${count} times`;
  });

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    // 对应类实现 componentDidMount 和 componentWillUnmount
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // Specify how to clean up after this effect:
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

1. **useEffect 做了什么？** 通过使用这个 Hook，你可以告诉 React 组件需要在渲染后执行某些操作。React 会保存你传递的函数（我们将它称之为 “effect”），并且在执行 DOM 更新之后调用它。

2. **useEffect 会在每次渲染后都执行吗？** 是的，默认情况下，它在**第一次渲染之后**和每次**更新**之后都会执行。

3. **为什么要在 effect 中返回一个函数？** 这是 effect 可选的清除机制。每个 effect 都可以返回一个清除函数（类似componentWillUnmount函数）。如此可以将添加和移除订阅的逻辑放在一起。它们都属于 effect 的一部分。

4. **React 何时清除 effect？** React 会在组件卸载的时候执行清除操作。而effect 在每次渲染的时候都会执行。这就是为什么 React 会在执行当前 effect 之前对上一个 effect 进行清除。

5. **可以使用多个effect？** 当然可以。如上面的例子，你可以使用多个effect封装不同的逻辑。

##### 通过跳过 Effect 进行性能优化

因为每次更新的时候都要运行 effect（类似运行componentDidUpdate），所以，如果状态并没改变，如何让React跳过effect的调用呢？

如果是componentDidUpdate，我们会利用prevProps 或 prevState：

```
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
    document.title = `You clicked ${this.state.count} times`;
  }
}
```
useEffect中，我们只要在该API中传入第二个数组参数即可：

```
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新

useEffect(() => {
  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  };
}, [props.friend.id]); // 仅在 props.friend.id 发生变化时，重新订阅
```

数值是全等笔记，count1 === count2

##### 提示

与 componentDidMount 或 componentDidUpdate 不同，使用 useEffect 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。大多数情况下，effect 不需要同步地执行。

