#### 组件通信

概括的讲，可以有如下几种类型：

| 通信类型 | 方式 | 
| ------ | ------ | 
| 父组件向子组件通信 | 通过props 向子组件传递需要的信息 | 
| 子组件向父组件通信 | 1.利用回调函数</br> 2.自定义事件机制（eg: 发布/订阅模式）|
| 跨级组件通信 | 1.通过props 向子组件传递需要的信息</br> 2.使用 context 来实现跨级父子组件间的通信 |
| 没有嵌套关系的组件通信 | 自定义事件机制（eg: 发布/订阅模式） |
| 适用于上述所有方式 | 利用数据管理框架：Redux|


#### 1. 利用props

看一个例子：
子组件是一个```select```下拉框，内容由父组件定义。当下拉框变动时，下面一行文字会显示相应的选择内容。

![](/assets/communication-1.webp)


```
//父组件
class A extends React.Component {
    constructor(props) {
        super(props);
        this.state = {text: "", value: ["nicole", "mike"]};
        this.handleSelect = this.handleSelect.bind(this);
    }

    handleSelect(event) {
        this.setState({text: event.target.value});
    }

    render() {
        return (<div>
            <B handleSelect={this.handleSelect} data={this.state.value}></B>
            <p>select value: {this.state.text}</p>
        </div>);
    }
}

//子组件B
class B extends React.Component {

    getOption(){
        return this.props.data.map((item)=>{
            return (<option value={item}>{item}</option>)
        })
    }

    render() {
        return (<select onChange={this.props.handleSelect}>
            {this.getOption()}
        </select>);
    }
}

ReactDOM.render(
    <A/>,
    document.getElementById('container')
);
``` 

从代码``` <B handleSelect={this.handleSelect} data={this.state.value}></B>```可以看出，组件A通过```props```向组件B传递了两个重要内容：```handleSelect```和```data```。
* ```data```：父组件定义了选项内容，将其传递给组件B，从而构造好B组件显示内容
* ```handleSelect```：B组件触发onChange事件之后，会调用函数handleSelect，从而委托调用组件A的handleSelect方法，更新组件B状态值```this.setState({text: event.target.value});```。

这样，父子组件就可以互相通信了。

#### 2.Context

** Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。**

Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据，例如当前认证的用户、主题或首选语言。

首先，定义Context Provider和Consumer:
```
import React, { createContext } from 'react';
const Context = createContext();

export class MyProvider extends React.Component {
    constructor() {
        super();
        // 全局享用的状态值
        this.state = {
            isDebugger: process.env.NODE_ENV === 'development',
            toggleLoading: false,
            message: ''
        };
        this.updateContext = this.updateContext.bind(this);
    }

    // 为state添加新的属性或者修改原state的值
    updateContext(newData) {
        this.setState(Object.assign({}, this.state, newData));
    }
    
    render() {
        const contextData = { data: this.state };
        const { toggleLoading, message } = contextData.data;
        // 在state外层的对象contextData上添加属性updateContext，值为函数
        Object.defineProperty(contextData, "updateContext", {
            value: this.updateContext
        });
        // 显示全局消息提示框
        if (message) {
            setTimeout(() => this.setState({ message: '' }), 1000)
        }
        return (
            <Context.Provider value={contextData}>
                {this.props.children}
                {toggleLoading && <Loading />}
                {message && <Message text={message} />}
            </Context.Provider>
        );
    }
}

// 消费组件
export const MyConsumer = Component => {
    return props => (
        <Context.Consumer>
            {context => {
                return <Component context={context} {...props} />;
            }}
        </Context.Consumer>
    );
};
```

第二步，给根组件添加Provider

```
ReactDOM.render(
    <MyProvider>
        <App />
    </MyProvider>,
    document.getElementById('root'));
```

第三步，哪个组件需要全局Context，就用消费组件包装

```
class UserPage extends React.Component {

    load(){
        this.props.context.updateContext({ toggleLoading: true });
        get()
        .then(res=>{
            this.props.context.updateContext({ toggleLoading: false });
        })
        .catch(error=>{
            this.props.context.updateContext({ message: 'Error!' });
        })
    }
}
export default MyConsumer(injectIntl(UserPage));
```