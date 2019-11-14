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

继承式高阶组件常用于渲染劫持，例如，当用户处于登陆状态时，允许组件渲染；否则渲染一个空组件。

```
function withAuth(WrappedComponent, newProps) {
    return class WrappingComponent extends WrappedComponent {
        render(){
            if (this.props.loggedIn) {
                this.props = {...this.props, ...newProps}
                return super.render();
            } else {
                return null;
            }
        }
    }
}
```

特点：
1. 只有一个生命周期
2. 可以对原组件的props进行增强或者删减
3. 渲染方式为 - `return super.render();`

#### 3. render prop 模式

前面两种高阶组件都会操作`props`，通过增减`props`而改变原组件功能。

render prop 模式不会操作组件的`props`，但是，它的硬性要求是：
1. 父组件必须有子组件
2. 子组件必须为函数

如下面的例子：
```javascript
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

顺着这个方式往下扩展，我们可以发现，这种父组件并没有创建出新的组件，而是将props属性向注入到原组件内。
这种设计模式就是“**依赖注入**”。当A依赖B时，并不要将A直接依赖B，而是将B以接口的形式传递给A（通过函数）。
所以，我们也可以让父组件不包含子组件，直接将通过props函数来渲染组件。

```javascript
const Auth(props) {
   const user = getUser();
   if (user.login){
      const allProps = {...user, ...props};
      return (
        <React.Fragment>
          {props.login(allProps)}
        </React.Fragment> )
   } else {
       return (
        <React.Fragment>
          {props.nologin(props)}
        </React.Fragment> )
   }
}

// usage
<Auth
   login={props=><Login ...props/>}>
   nologin={props=><NoLogin ...props/>}
/>
```

#### 4. MixIn

应用场景：只能在React.createClass方式创建的组件类中使用，不能通过ES6 Class创建的组件中使用。

MixIn是一种反模式的设计，它可以继承多个组件，包括其内部状态state。所以，很容易造成state混乱，官方不建议使用。

#### 5. 注意事项

1. 不要在组件的render中使用高阶组件。因为调用高阶组件，每次都会返回一个新组件，所以，每次render，前一次高阶组件创建的组件都会被卸载，然后重新挂载，既影响效率，有丢失了组件及其子组件的状态。**高阶组件适合在组件外部使用**。
```
// 不好的应用场景
render(){
    // 每次render，enhance都会创建一个新组件，尽管被包装组件没有变化
    const EnhancedComponent = enhance(MyComponent);
    // 因为是新组件，所以会经历旧组件的卸载和新组件的重新挂载
    return <EnhancedComponent />
}
```
2. 高阶组件和父组件很类似。区别在于：**高阶组件是一个函数，关注逻辑；父组件是一个组件，关注UI/DOM。**如果逻辑和DOM不相关（如数据校验，请求发送等），那么这部分逻辑适合放在高阶组件里。


