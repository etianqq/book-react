#### MobX 简单可扩展的状态管理方案

MobX是Redux之后的一个状态管理库，基于**响应式**管理状态，整体是一个观察者模式的架构。

* state是被观察者
* 使用store的组件是观察者

MobX可以有多个store对象，store使用的state是可变对象。

```
Redux只有一个store（可以分为多个子store），state是不可变对象！
```

#### 1. 定义store

```
import { observable } from "mobx";

class Todo {
    id = Math.random();
    @observable title = "";
    @observable finished = false;
    // 计算属性
    @computed get unfinishedTodoCount() {
        return this.todos.filter(todo => !todo.finished).length;
    }
}
```

#### 2. 定义使用store的组件

```
import React, {Component} from 'react';
import ReactDOM from 'react-dom';
import {observer} from 'mobx-react';

@observer
class TodoListView extends Component {
    render() {
        return <div>
            <ul>
                {this.props.todoList.todos.map(todo =>
                    <TodoView todo={todo} key={todo.id} />
                )}
            </ul>
            Tasks left: {this.props.todoList.unfinishedTodoCount}
        </div>
    }
}

const TodoView = observer(({todo}) =>
    <li>
        <input
            type="checkbox"
            checked={todo.finished}
            onClick={() => todo.finished = !todo.finished}
        />{todo.title}
    </li>
)

const store = new TodoList();
ReactDOM.render(<TodoListView todoList={store} />, document.getElementById('mount'));
```

observer 会将 React (函数)组件转换为它们需要渲染的数据的衍生。 

使用 MobX 时没有所谓的智能和无脑组件。 所有的组件都会以巧妙的方式进行渲染，而只需要一种简单无脑的方式来定义它们。MobX 会确保组件总是在需要的时重新渲染，但仅此而已。

所以上面例子中的 onClick 处理方法会强制对应的 TodoView 进行渲染，如果未完成任务的数量(unfinishedTodoCount)已经改变，它将导致 TodoListView 进行渲染。 可是，如果移除 Tasks left 这行代码(或者将它放到另一个组件中)，当点击 checkbox 的时候 TodoListView 就不再重新渲染。

更多学习可参考：https://cn.mobx.js.org/