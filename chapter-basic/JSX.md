#### JSX语法

JSX是一种类XML格式的语法，它可以使用JavaScript语法，也可以使用HTML语法来创建虚拟元素（虚拟DOM树）。

####1. 基本语法
JSX采用标签来实现任意嵌套，这点和XML/HTML类似。比如，构造一个列表：
```
var MyList = (
  <ul>
    <li>item1</li>
    <li>item2</li>
  </ul>);
// 后续可通过标签<MyList />使用该组件
```
要注意下面几点：
1. 定义标签时，最外层只能有一个标签，即root标签只允许定义一个。下面的写法是错误的：
```
 var MyList =(
  <h1>this is a list</h1>
  <ul>
    <li>item1</li>
    <li>item2</li>
  </ul>);
```

如何减少DOM嵌套：
* React 16之后，提供了`React.Fragment`组件，其可以在不额外创建DOM元素的情况下，让`render()`方法返回多个元素。
* 可以通过数组的方式返回多个组件，每个数组项都必须有唯一属性`key`

```
1. 利用<React.Fragment>组件
return (
    <React.Fragment>
      Some text.
      <h2>A heading</h2>
    </React.Fragment>
  );
}
2. 返回数组类型
return (
    [
        <Component1 key='1'>,
        <Component2 key='2'>,
    ]
  );
}
```

2. 标签必须闭合，严格遵守XML语法。下面的写法是错误的：
```
var MyList = (
<ul>
  <li>item1</li>
  <li>item2</li>);
```
3. 根据标签第一个字母大小写情况，可以分为两种元素：**DOM元素和组件元素**。
  * 首字母为小写时，是DOM元素，即原生的HTML标签。
  * 首字母为大写时，是组件元素。

#### 2. 元素属性
上一个小结提到了有两种元素，**DOM元素和组件元素**。

先讲述下两种元素在处理属性时的不同之处。

| 属性       | DOM元素                                                      | 组件元素             |
| ---------- | ------------------------------------------------------------ | -------------------- |
| 自定义     | 需要加```data-```前缀。如：```<h1 data-attr="xxx"></h1>```   | 可以随意定义属性名称 |
| class和for | 因为这两个名字为JavaScript关键字，因此需要转换为```className```和```htmlFor```之后再使用 | 可以直接使用该属性名 |

然后分析一下两种元素处理属性时相同之处。

(1) JavaScript表达式
可以使用JavaScript属性表达式。用```{}```代替```""```即可，格式为```{javaScript code}```。
```
<MyList name={isLogin ? name: "user"} />
```

(2) 展开属性
使用“展开属性”来定义全部属性。如果元素属性特别多，建议采用这种格式。

```
const data = {name: 'button', value='1' text='it is a button'};
let component = <Component name={data.name} value={data.value} text={data.text} />
更简练的写法为：
let component = <Component {...data} />
```

(3) `dangerouslySetInnerHTML`属性 
React会将所有显示在DOM中的字符串转义，防止XSS攻击。如果组件子元素为HTML，那么，可以通过```dangerouslySetInnerHTML```属性设置。

```
var MyList = (
<ul>
  <li data-attr="{a}" dangerouslySetInnerHTML={{__html: '<h1>item1</h1>'}}></li>
</ul>)
```

(4) ref属性
通过ref属性可以得到DOM/组件实例。
* 将ref赋值给变量：
  * 如果在原生HTML标签上使用ref属性，返回DOM对象；
  * 如果是在自定义组件标签上使用ref属性，则返回该组件对象；
* 将ref赋值给函数，当组件完成装载时会调用这个函数，参数是这个元素的DOM对象。

参考下述例子：
```
class Comp extends React.Component {
    constructor() {
        this.subCompRef = React.createRef();
    }

    componentDidMount() {
        // 通过this.refs访问ref属性值
        console.log("this.subCompRef:" + this.subCompRef);
    }

    render() {
        return (<SubComp ref={this.subCompRef}/>)
    }
}
class SubComp extends React.Component {

    render() {
        return (<h1>Hello world!</h1>)
    }
}
```
控制台打印值为：
```
{current: div.builder-block}
```
#### 小结
了解了JSX语法之后，我们就可以开始尝试创建React组件了。
