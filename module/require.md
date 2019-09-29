#模块载入

####1.模块分类

* 原生（核心）模块
* 文件模块
    * .js。通过fs模块同步读取js文件并编译执行。
    * .node。通过C/C++进行编写的Addon。通过dlopen方法进行加载。
    * .json。读取文件，调用JSON.parse解析加载。
    
原生模块在Node.js源代码编译的时候编译进了二进制执行文件，加载的速度最快。
文件模块是动态加载的，加载速度比原生模块慢。

但是Node.js对原生模块和文件模块都进行了缓存，于是在第二次require时，是不会有重复开销的。    

####2. require方法中的文件查找策略

![](/assets/require module.jpg)

####3. 从文件加载模块

当文件模块缓存中不存在，而且不是原生模块的时候，Node.js会解析require方法传入的参数，并从文件系统中加载实际的文件。

#####(1)paths属性
对于每一个被加载的文件模块，创建这个模块对象的时候，这个模块便会有一个```paths```属性，其值根据当前文件的路径计算得到。我们创建modulepath.js文件时，module.path的值为：
```
D:\project_git\top.sass.web\script_gulp\node_modules,
D:\project_git\top.sass.web\node_modules,
D:\project_git\node_modules,
D:\node_modules;

```
#####(2)require绝对路径的文件
查找时不会去遍历每一个node_modules目录，其速度最快。

#####(3)require相对路径的文件

1. 从module path数组中取出第一个目录作为查找基准。
2. 直接从目录中查找该文件，如果存在，则结束查找。如果不存在，则进行下一条查找。
3. 尝试添加.js、.json、.node后缀后查找，如果存在文件，则结束查找。如果不存在，则进行下一条。
4. **尝试将require的参数作为一个包来进行查找，读取目录下的package.json文件，取得main参数指定的文件。**
5. 尝试查找该文件，如果存在，则结束查找。如果不存在，则进行第3条查找。
6. 如果继续失败，则取出module path数组中的下一个目录作为基准查找，循环第1至5个步骤。
7. 如果继续失败，循环第1至6个步骤，直到module path中的最后一个值。
8. 如果仍然失败，则抛出异常。

####4.Node.js模块与前端模块的异同

* Node.js将JavaScript文件载入到最终的执行中，进行了包装，使得每个文件中的变量天然的形成在一个闭包之中，不会污染全局变量。
* 浏览器端则通常是裸露的JavaScript代码片段（浏览器端通过script标签的载入JavaScript文件）。

所以为了解决前后端一致性的问题，类库开发者需要将类库代码包装在一个闭包内。以下代码片段抽取自著名类库underscore的定义方式（注意，```this```是浏览器环境下的```window```对象）。

```
(function () {
    // Establish the root object, `window` in the browser, or `global` on the server.
    var root = this;
    var _ = function (obj) {
            return new wrapper(obj);
        };
    if (typeof exports !== 'undefined') {
        if (typeof module !== 'undefined' && module.exports) {
            exports = module.exports = _; // 把module.exports赋值给exports
        }
        exports._ = _;
    } else if (typeof define === 'function' && define.amd) {
        // Register as a named module with AMD.
        define('underscore', function () {
            return _;
        });
    } else {
        root['_'] = _;
    }
}).call(this);
```