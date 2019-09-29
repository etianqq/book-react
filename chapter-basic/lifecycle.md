#### 生命周期

React的核心是组件，组件在创建和渲染的过程中，需要调用固定的钩子函数，也称为组件的“生命周期”。利用生命周期函数，可以做初始化工作，并在渲染过程中实现一些特定功能。

** 类组件才有生命周期 **

####1. React v16.3之前的生命周期函数

![](/assets/react-lifecycle-old.png)

```
shouldComponentUpdate 是一个非常重要的钩子函数，这个函数默认返回true。

在React中，调用setState方法，React不会立即对其更新，而是将其标记为“脏”状态
（组件状态更新不会立刻生效，React使用事件轮询对变更内容进行批量绘制）。
当事件轮询结束后，React将“脏”组件及其子节点进行重绘，所有后代节点的render方法都会被调用，哪怕它们没法发生变化。

通过shouldComponentUpdate方法，可以阻止子树的重绘
（自行实现该方法并返回false，React会跳过该组件及其子组件的重绘过程）。

--- 参考《Pro React》
```
下面，我们来看一个真实例子，观察组件生命周期的变换（采用ES6类模式）。

```
class DangerButton extends React.Component {
    /*类型检查*/
    static propTypes = {
        onClick: React.PropTypes.func,
        text: React.PropTypes.string
    };

    /*初始化props值*/
    static defaultProps = {
        type: 'btn'
    };

    constructor(props) {
        super(props);
        // 初始化state值
        this.state = {count: 0};
        this.increaseCount = this.increaseCount.bind(this);
    }

    increaseCount() {
        this.setState({count: this.state.count + 1});
    }

    getObjectValues(obj){
        var array = [];
        for(let key in obj){
            array.push(key + ":" + obj[key]);
        }
        return array.join(";");
    }

    /*----------------start: life cycle---------------*/
    /*组件被注入DOM之前*/
    componentWillMount() {
        var button = document.getElementById('dangerBtn');
        console.log("componentWillMount:" + button);
    }
    /*组件被注入DOM之后*/
    componentDidMount() {
        var button = document.getElementById('dangerBtn');
        console.log("componentDidMount:" + button);
    }

    /*挂载的组件接收到新的props时被调用*/
    componentWillReceiveProps(nextProps){
        console.log("componentWillReceiveProps:" + nextProps);
    }

    /*指定是否更新props和state*/
    shouldComponentUpdate(nextProps, nextState){
        console.log("shouldComponentUpdate-true!");
        return true;
    }

    /*更新组件时，渲染之前*/
    componentWillUpdate(nextProps, nextState){
        console.log("componentWillUpdate-nextProps:" + this.getObjectValues(nextProps));
        console.log("componentWillUpdate-nextState:" + this.getObjectValues(nextState));
    }

    /*更新组件时，渲染之后*/
    componentDidUpdate(prevProps, prevState){
        console.log("componentDidUpdate-prevProps:" + this.getObjectValues(prevProps));
        console.log("componentDidUpdate-prevState:" + this.getObjectValues(prevState));
    }

    /*卸载组件*/
    componentWillUnMount() {
        var button = document.getElementById('dangerBtn');
        console.log("componentDidMount:" + button);
    }

    render() {
        console.log("rendering....");
        return (<div>
            <button id="dangerBtn" className='red' onClick={this.increaseCount}>
                <span className='white'>{this.props.text}</span>
            </button>
            <p>Click count: {this.state.count}</p>
        </div>);
    }
    /*----------------end: life cycle---------------*/
}
ReactDOM.render(
    <DangerButton text="click it!"/>,
    document.getElementById('container')
);
```
第一次渲染DangerButton组件时，控制台打印如下信息：
```
componentWillMount:null
rendering....
componentDidMount:[object HTMLButtonElement]
```
可见，渲染组件的```componentWillMount```阶段，真实DOM还没有生成；到了```componentDidMount```阶段，组件才真正被加载到DOM中。

然后，点击DangerButton，```count```值加一，控制台打印如下信息：
```
shouldComponentUpdate-true!
componentWillUpdate-nextProps:text:click it!;type:btn
componentWillUpdate-nextState:count:1
rendering....
componentDidUpdate-prevProps:text:click it!;type:btn
componentDidUpdate-prevState:count:0
```
可见，如果组件自身的state更新后（点击button，触发onClick事件），会依次执行```shouldComponentUpdate```,```componentWillUpdate```，```render```和```componentDidUpdate```函数。

####2. React v16.3之后的生命周期函数

![](/assets/react-lifecycle-new.png)

引入了两个新的生命周期函数：
* `getDerivedStateFromProps`
* `getSnapshotBeforeUpdate`

deprecate了一组生命周期API：
* `componentWillReceiveProps`
* `componentWillMount`
* `componentWillUpdate`


getDerivedStateFromProps() 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。此方法适用于罕见的用例，即 state 的值在任何时候都取决于 props。慎用！

getSnapshotBeforeUpdate() 在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。此生命周期的任何返回值将作为参数传递给 componentDidUpdate()。