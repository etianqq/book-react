props和state都用于描述组件特性，但是，两者有本质区别。前者是由父组件定义的属性变量，后者是组件本身持有的变量。并且，props一旦被定义，就不会再更改；但是，state会随着交互变化而变化。

下面，逐一分析。

####1. props
props是properties的缩写，顾名思义，就是属性变量。**props用于在父子组件之间传递信息**，这种传递是单向的，从父组件到子组件。props一旦被定义，就不可以再修改。

####2.state
**state是组件维护自身状态的变量**，当state更改时，组件会尝试重新渲染。这也充分说明了React数据和模板是单向绑定，数据变化驱动模板更新。

更新state值需要调用组件接口```setState```。

####3. 实例

与交互无关的数据一般都定义在props中并渲染出来，对于用户输入，服务器请求或者其他交互变化的响应，需要用state来维护。

下面是一个简单的例子（在Input里面输入任意字符，点击button，会将输入的文字显示在Input框下部，用```<li>```标签显示）。

![](/assets/state-1.webp)

```
class Dashboard extends React.Component {
    constructor(props) {
        super(props);
        // 初始化state值
        this.state = {data: [], newItem: ""};
        // 事件函数需要指定调用对象
        this.updateNewItem = this.updateNewItem.bind(this);
        this.addItem = this.addItem.bind(this);
    }

    // 更新state.newItem的值
    updateNewItem(event) {
        this.setState({newItem: event.target.value});
    }

    // 更新state.newItem和state.data的值
    addItem(event) {
        this.state.data.push(this.state.newItem);
        this.setState({newItem: ""});
    }

    render() {
        return (<div className="dashboard">
            <input type="text" value={this.state.newItem} onChange={this.updateNewItem}/>
            <button onClick={this.addItem}>{this.props.text}</button>

            <ItemList data={this.state.data}/>
        </div>);
    }
}

class ItemList extends React.Component {
    render() {
        var listData = this.props.data.map((item, index) => {
            return (<Item text={item} name="item" color="blue" key={index}/>);
        });
        return (<ul>{listData}</ul>);
    }
}

class Item extends React.Component {
    // 根据父组件传递的属性值props渲染子组件
    render(){
        return (<li name={this.props.name} className={this.props.color}>{this.props.text}</li>);
    }
}

ReactDOM.render(
    <Dashboard text="Add!"/>,
    document.getElementById('container')
);
```

####4. props.children

props.children可以访问到组件的子组件集合。如果只有一个子组件，那么返回该子组件对象；如果有多个子组件，则返回包含所有子组件的集合对象。

React提供了帮助函数React.Children.XXX来操作```props.children```集合对象，帮助函数有：```map```，```forEach```，```count```等。

下面是一个创建按钮组件的例子，利用React.Children.map遍历子组件并给子组件添加统一的属性值。

![](/assets/state-2.webp)

```
<style>
    .blue {  color: blue;  }
</style>

class ButtonGroup extends React.Component {
    constructor(props) {
        super(props);
    }

    renderChildren() {
        // 利用React.Children.map遍历所有子组件<ButtonOption>对象
        return React.Children.map(this.props.children, (child) => {
            // 复制子组件，并且给全部子组件添加新属性{color: "blue"}
            return React.cloneElement(child, {
                color: "blue"
            })
        });
    }

    render() {
        return (<div>{this.renderChildren()}</div>);
    }
}

class ButtonOption extends React.Component {
    render() {
        return (<button className={this.props.color} value={this.props.value}>{this.props.text}</button>);
    }
}

ReactDOM.render(
    <ButtonGroup>
        <ButtonOption text="left" value="0"></ButtonOption>
        <ButtonOption text="center" value="1"></ButtonOption>
        <ButtonOption text="right" value="2"></ButtonOption>
    </ButtonGroup>,
    document.getElementById('container')
);
```