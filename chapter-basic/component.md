React创建组件有两种模式，原生JavaScript和JSX语法，后者是React推荐的构组件方式，因为，它能更加精确得定义包含属性的树状结构。

####1. 使用原生JavaScript创建组件
React框架提供了创建虚拟DOM的接口，``` React.createElement```，参数定义如下：
```
ReactElement createElement(
  string/ReactClass type,  --- 标签名
  [object props],   --- 属性
  [children ...]   --- 子节点
)
```
例如，创建一个红色的表示危险信号的按钮组件-DangerButton:
```
<script>
    var TextSpan = React.createElement('span', {className: 'white'}, "Danger");
    var DangerButton = React.createElement("button", {className: 'red'}, TextSpan);
    ReactDOM.render(
            DangerButton,
            document.getElementById('container')
    );
</script>
```
ReactDOM是渲染虚拟DOM组件的入口。

实际上，JSX最终被编译为可执行的JavaScript代码，就是由``` React.createElement()```来创建DOM组件。

####2. 使用JSX语法创建组件
JSX语法创建的组件，以标签形式使用，这能让复杂的树型更易于阅读，优于纯JavaScript语法。并且，JSX也遵循JavaScript语义。

一般，有两种方式：方法创建和类创建。

######(1) React.createClass
使用```React.createClass```方法创建组件非常简单，关键点就是定义```render```函数，这个函数用于定义虚拟DOM。

还以上面例子中的DangerButton为例，使用```React.createClass```创建：
```
<script type="text/babel">
    var DangerButton = React.createClass(
        {
            //JSX语法
            render(){
                return (<button className='red'><span className='white'>Danger</span></button>);
            }
        });
    ReactDOM.render(
            <DangerButton/>,
            document.getElementById('container')
    );
</script>
```
######(2) 类创建
使用ES6中```class```语法来创建组件，这种方式是React官方推荐的。因为，对于复杂组件，类模式能更加清晰和简明的定义组件功能已经数据处理。

用ES6语法来创建DangerButton：
```
<script type="text/babel">
    class DangerButton extends React.Component {
        render() {
            return (<button className='red'><span className='white'>Danger</span></button>);
        }
    }
    ReactDOM.render(
            <DangerButton/>,
            document.getElementById('container')
    );
</script>
```
现在可能还感觉不到类模式组件的强大之处，等到了实际应用中，构造复杂组件时，会体会它的优势。

######(3) 无状态函数
有一种纯函数态的组件，没有保持任何内部状态，没有生命周期方法，这样的组件，可以用函数定义。

其实，DangerButton就是这样的组件。
```
function DangerButton(props) {
  return <button className="red"><span className="white">{props.name}</span></button>;;
}
ReactDOM.render(<HelloMessage name="danger" />, mountNode);
```
理想世界里组件都应该是无状态函数，因为这种模式可以避免做内存分配优化等额外工作，如果可能，这是推荐模式。

#### 3. “容器组件”+“展示组件” = “组件”

这是创建组件的一种模式，如果引入redux，推进按照这种模式设计组件。

* 容器组件： 外层组件，负责和Redux Store通信，处理数据。
* 展示组件（傻瓜组件）：内层组件，只专心负责渲染界面，根据props来传递数据。

“傻瓜组件”一般都是无状态函数。

####小结
后续我们将进一步探讨React组件的生命周期函数。
