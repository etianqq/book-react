#### 性能优化

* 在 HTML 内实现 Loading 态或者骨架屏；
* 去掉外联 css；
* 缓存基础框架 - HTTP 缓存资源；
* 使用动态 polyfill；
* 使用 SplitChunksPlugin 拆分公共代码；
* 正确地使用 Webpack 4.0 的 Tree Shaking - 在引入这个模块，却没有使用它时，webpack 会自动把它 Tree Shaking 丢掉；
* 使用动态 import，切分页面代码，减小首屏 JS 体积；
    * React Loadable 是一个专门用于动态 import 的 React 高阶组件，你可以把任何组件改写为支持动态 import 的形式。
* 编译到 ES2015+，提高代码运行效率，减小体积；
* 使用 lazyload 和 placeholder 提升加载体验。


#### 代码上的优化

* 列表项定义key属性
    * 不推荐用索引作为key，因为一旦列表中的数据发生重排，数据的索引也会发生变化
    * key只要不在当前列表中重复即可
    
* 组件的属性值尽量不要用内联函数，如<Com1 action={()=>doSomething()}/>

* 尽量把style提到组件之外，不要直接写在JSX里面。因为如果style里直接定义样式对象，会导致组件每次渲染都要创建一个新的style对象。

    ```javascript
    // bad
    render(){
    	return (<div style={{color: 'red'}}>hello</div>)
    }
    // good
    const style = {color: 'red'}
    ...
    render(){
    	return (<div>hello</div>)
    }
    ```

* 利用`shouldComponentUpdate`定义更新条件

    * 适当时使用`React.PureComponent`，其自带`shouldComponentUpdate`优化，会对props进行浅比较。

        ```javascript
        class Comp extends React.Component {
         	render(){
        		return (<div>{this.props.name}</div>)
        	}
        }
        ```

    * 函数组件可以利用`React.memo`实现`shouldComponentUpdate`优化，同样是浅比较。

        ```javascript
        const MyComponent = React.memo(props => {
          /* render using props */
          return (
            <div>{props.name}</div>
          );
        });
        ```

* 利用`useMemo`缓存复杂计算的值，利用`useCallback`缓存函数

    ```javascript
    // useMemo
    // 使用useMemo来执行昂贵的计算，然后将计算值返回，并且将count作为依赖值传递进去。
    // 这样，就只会在count改变的时候触发expensive执行，在修改val的时候，返回上一次缓存的值。
    export default function WithMemo() {
        const [count, setCount] = useState(1);
        const [val, setValue] = useState('');
        const expensive = useMemo(() => {
            console.log('compute');
            let sum = 0;
            for (let i = 0; i < count * 100; i++) {
                sum += i;
            }
            return sum;
        }, [count]);
    
        return <div>
            <h4>{count}-{expensive}</h4>
            {val}
            <div>
                <button onClick={() => setCount(count + 1)}>+c1</button>
                <input value={val} onChange={event => setValue(event.target.value)}/>
            </div>
        </div>;
    }
    
    // useCallback
    // 借助useCallback来返回函数，然后把这个函数作为props传递给子组件；
    // 这样，子组件就能避免不必要的更新。
    import React, { useState, useCallback, useEffect } from 'react';
    function Parent() {
        const [count, setCount] = useState(1);
        const [val, setVal] = useState('');
    
        const callback = useCallback(() => {
            return count;
        }, [count]);
        return <div>
            <h4>{count}</h4>
            <Child callback={callback}/>
            <div>
                <button onClick={() => setCount(count + 1)}>+</button>
                <input value={val} onChange={event => setVal(event.target.value)}/>
            </div>
        </div>;
    }
    
    function Child({ callback }) {
        const [count, setCount] = useState(() => callback());
        useEffect(() => {
            setCount(callback());
        }, [callback]);
        return <div>
            {count}
        </div>
    }
    ```

* React官方建议把state当作不可变对象。当组件的状态都是不可变对象时，`shouldComponentUpdate`只需**浅比较**就可以判断状态是否真的改变，从而避免不必要的render调用。
    * 状态类型是不可变类型 - number, string, boolean, null, undefined
    * 状态类型是array，创建新的数组返回（concat, slice, filter, map 会返回一个新数组）：
       ```javascript
       // add
       this.setState({
           books: [...preState.books, 'New book']
       })
       // remove
       this.setState({
           books: preState.books.slice(1, 3)
       })
       // filter
       this.setState({
           books: preState.books.filter(item=>item !== 'React)
       })
       ```
    * 状态类型是object，创建新的对象返回（Object.assign，对象扩展语法，或者Immutable库）
        ```javascript
        this.setState({
           owner: Object.assgin({}, preState.owner, {name: 'Jason'})
       })

        this.setState({
            owner: {...preState.owner, name: 'Jason'}
        })
       ```
    