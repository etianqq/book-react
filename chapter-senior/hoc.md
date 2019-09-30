#### 高阶组件

高阶组件（Higher Order Component,HOC）是React的一种设计模式，用于增强现有组件的功能。

一个高阶组件就是一个函数，这个函数的输入为组件，输出为另一新组件。

根据输入组件和输出组件参数的关系，可以分为：
* 代理方式的高阶组件
* 继承方式的高阶组件
* 函数式子组件
* Mixin（不要使用）

#### 1. 代理式高阶组件（推荐）

例子如下：
```
// 为原组件WrappedComponent增加新的属性newProps
function addNewProps(WrappedComponent, newProps) {
    return class WrappingComponent extends React.Component {
        render(){
            return <WrappedComponent {...this.props} {...newProps}>
        }
    }
}
```

特点：
1. 高阶组件和被包裹组件有各自的生命周期；
2. 可对原组件的props进行增强或者删减
3. 渲染方式为 - `return <WrappedComponent {...otherProps}/>`

#### 2.继承式高阶组件

```
// 重写上面的例子
function addNewProps(WrappedComponent, newProps) {
    return class WrappingComponent extends WrappedComponent {
        render(){
            this.props = {...this.props, ...newProps}
            return super.render();
        }
    }
}
```

特点：
1. 只有一个生命周期
2. 可以对原组件的props进行增强或者删减
3. 渲染方式为 - `return super.render();`

#### 3. 函数式子组件

前面两种高阶组件都会操作`props`，通过增减`props`而改变原组件功能。

函数式子组件不会操作组件的`props`，但是，它的硬性要求是：
1. 父组件必须有子组件
2. 子组件必须为函数

如下面的例子：
```
// 定义组件
class AddUserProp extends React.Component {
    render(){
        const user = 'mock user';
        return this.props.children(user);
    }
}

// 使用该组件
<AddUserProp>
{(user)=><div>user</div>}
</AddUserProp>
```

因为子组件是函数，所以这种模式非常灵活。

#### MixIn

应用场景：只能在React.createClass方式创建的组件类中使用，不能通过ES6 Class创建的组件中使用。

MixIn是一种反模式的设计，它可以继承多个组件，包括其内部状态state。所以，很容易造成state混乱，官方不建议使用。


