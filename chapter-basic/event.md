#### 事件

React 基于Virtual DOM 实现了一个 SyntheticEvent （合成事件）层，我们所定义的事件处理器会接收到一个 SyntheticEvent 对象的实例，它完全符合W3C 标准，不会存在任何IE 标准的兼容性问题。并且与原生的浏览器事件一样拥有同样的接口，同样支持事件的冒泡机制，我们可以使用 stopPropagation() 和 preventDefault() 来中断它。
所有事件都自动绑定到最外层上。
