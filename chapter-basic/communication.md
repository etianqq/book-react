父子组件直接通信，主要靠```props```实现。

* 父组件通过```props```向子组件传递数据和方法
* 子组件触发事件，通过```props```上传递的事件，委托调用父组件函数，从而更新父组件状态值。

如果有更复杂的组件通信，需要使用[Redux](http://www.redux.org.cn/)。

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

####小结
到了这里，react基础知识就介绍完了。可见，react框架涉及到的API和内置属性并不多，它的难点在于如何将一个UI界面合理分割为若干组件进行组合嵌套，并且，数据如何在组件间传递，变化。