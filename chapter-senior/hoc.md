#### 高阶组件

高阶组件（Higher Order Component,HOC）是React的一种设计模式，用于增强现有组件的功能。

一个高阶组件就是一个函数，这个函数的输入为组件，输出为另一新组件。

根据输入组件和输出组件参数的关系，可以分为：
* 代理方式的高阶组件
* 继承方式的高阶组件

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

其特点：
1. 高阶组件和被包裹组件有各自的生命周期；
2. 可对原组件的props进行增强或者删减

#### 2.继承式高阶组件