#exports

![](/assets/exports-definition.png)

####exports使用
导出的是一个模块**对象**。

使用时，必定都是```obj.xxx```格式。
```
===>foo.js:
exports.a = function(){
    console.log('a')
}
exports.a = 1 
 
===>test.js
var x = require('./foo');
console.log(x.a); // 1
```
####module.exports使用
导出的是任何合法的**一个JavaScript基本数据类型/对象** - boolean，number，date，JSON， string，function，array和其他。


module.export =View, 即相当于导出整个view对象。外面模块调用它的时候，能够调用View的所有方法。不过需要注意，只有是View的静态方法的时候，才能够被调用，prototype创建的方法，则属于View的私有方法。

```
===>foo.js
 function View(){}

 View.prototype.test = function(){
  console.log('test')
 }

 View.test1 = function(){
  console.log('test1')
 }
 module.exports = View
===>test.js

 var x = require('./foo');

 console.log(x) //{ [Function: View] test1: [Function] }
 console.log(x.test) //undefined
 console.log(x.test1) //[Function]
 x.test1() //test1
```

####module.exports与exports的区别

注意：每一个node.js执行文件，都自动创建一个module对象，同时，module对象会创建一个叫exports的属性，初始化的值是 {}

* module.exports 初始值为一个空对象 {}
* exports 是指向的 module.exports 的引用:```exports = module.exports = {};```
* require() 返回的是 module.exports 而不是 exports（如果运行时让exports和module.exports指向不同的对象，只有module.exports指向的对象才回被导出）
* module.exports才是真正的接口，exports只不过是它的一个辅助工具