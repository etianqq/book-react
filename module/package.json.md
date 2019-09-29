# package.json

| 属性名 | 说明 |
| :--- | :--- |
| name | 包名，需要在NPM上是唯一的。不能带有空格 |
| description | 包简介。通常会显示在一些列表中 |
| version | 版本号。一个语义化的版本号（[http://semver.org/](http://semver.org/) ），通常为x.y.z。该版本号十分重要，常常用于一些版本控制的场合|
| keywords | 关键字数组。用于NPM中的分类搜索 |
| maintainers | 包维护者的数组。数组元素是一个包含name、email、web三个属性的JSON对象 |
| contributors | 包贡献者的数组。第一个就是包的作者本人。在开源社区，如果提交的patch被merge进master分支的话，就应当加上这个贡献patch的人。格式包含name和email|
| bugs | 一个可以提交bug的URL地址。可以是邮件地址（mailto:mailxx@domain），也可以是网页地址 |
| licenses | 包所使用的许可证 |
| repositories | 托管源代码的地址数组 |
| dependencies | 当前包需要的依赖。这个属性十分重要，NPM会通过这个属性，帮你自动加载依赖的包 |
| scripts | 包管理器（NPM）在对包进行安装或者卸载的时候需要进行一些编译或者清除的工作，scripts字段的对象指明了在进行操作时运行哪个文件，或者执行拿条命令 |

 
例子：
1.contributors 
```
"contributors": [{
    "name": "Jackson Tian",
    "email": "mail @gmail.com"
    }, {
    "name": "fengmk2",
    "email": "mail2@gmail.com"
}],
```

2.licenses。包所使用的许可证。例如：

```
"licenses": [{
    "type": "GPLv2",
    "url": "http://www.example.com/licenses/gpl.html",
}]
```

3.scripts 
```
"scripts": {
    "install": "install.js",
    "uninstall": "uninstall.js",
    "build": "build.js",
    "doc": "make-doc.js",
    "test": "test.js",
}
```
