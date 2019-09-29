# global对象

#### 查看global

打开一个终端，启动一个node命令提示界面：

```
$ node
```

在命令提示界面下看看关于global的所有信息：

```
> global 
```

#### 全局属性

| 属性 | 类型 | 说明 |
| :--- | :--- |
| process | 对象 | 用于进程相关处理的对象 |
|　console　|　对象 |用于控制台标准备输出、标准错误等相关处理的对象　|
|　Buffer　|　对象 |是一个二进制数据处理类，用于处理缓冲区、编码/解码二进制数据等　|
|　module　|　对象 |用于Node模块的相关处理　|
|　exports　|　对象 |exports对象是对module.exports对象的引用　|
|　require　|　对象 |require对象是对module.require的进一步实现在，在module.require方法的基础上，require对象又引入模块路径解析、模块缓等。require实际上并非全局的而是模块作用域的。　|
|　__filename　|变量 |　当前模块文件的绝对路径 |
|　__dirname | 变量 | 模块文件所在文件夹的路径　|
|　setTimeout |　方法| 计时器相关 |
|　clearTimeout|　方法| 计时器相关 |
|　setInterval|　方法| 计时器相关 |
|　clearInterval|　方法| 计时器相关 |

```
console.log(__filename);
// /Users/liuht/example.js

console.log(__dirname);
// /Users/liuht
```



















