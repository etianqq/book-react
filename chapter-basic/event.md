#### 事件

React 基于Virtual DOM 实现了一个 SyntheticEvent （合成事件）层，我们所定义的事件处理器会接收到一个 SyntheticEvent 对象的实例，它完全符合W3C 标准，不会存在任何IE 标准的兼容性问题。并且与原生的浏览器事件一样拥有同样的接口，同样支持事件的冒泡机制，我们可以使用 stopPropagation() 和 preventDefault() 来中断它。
**所有事件都自动绑定到根结点。** 当事件被触发后，React将它映射到适当的组件元素。当组件被卸载时，React还会自动移除对应的事件监听器。

|  | react合成事件 | javascript原生事件 |
| ------ | ------ | ------ |
| 事件传播与阻止事件传播 | 目标对象处理=>事件冒泡 | 事件捕获阶段=>目标对象处理=>事件冒泡 |
| 事件类型 | 原生DOM事件的一个子集 | DOM事件集合 |
| 事件绑定方式 | `<button onClick={this.handleClick}>Test</button>`| 1.`<button onclick="alert(1);">Test</button>`<br>2.`el.onclick = e => { console.log(e); }`<br>3.`el.addEventListene r('click', () => {}, false);`|
| 事件对象 | 无兼容性问题 | W3C 标准和IE 标准下存在着差异。在低版本的IE 浏览器中，只能使用 `window.event`来获取事件对象 |
