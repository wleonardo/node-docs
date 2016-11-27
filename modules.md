### Table of Contents
* Modules
    * Accessing the main module
    * Addenda: Package Manager Tips
    * All Together...
    * Caching
        * Module Caching Caveats
    * Core Modules
    * Cycles
    
# Modules
    稳定性: 3 - locked
    Node.js has a simple module loading system. In Node.js, files and modules are in one-to-one correspondence. As an example, foo.js loads the module circle.js in the same directory.
Node.js 有一个简单的模块加载系统。在Node.js中文件和模块是一一对应的。例如，`foo.js`加载了同级目录下的`circle.js`模块

`foo.js`的内容

```
const circle = require('./circle.js');
console.log(`The area of a circle of radius 4 is ${circle.area(4)}`);
```

`circle.js`的内容

```
const PI = Math.PI;

exports.area = (r) => PI * r * r;

exports.circumference = (r) => 2 * PI * r;
```

`circle.js`模块输出了`area()`和`circumference()`函数。你可以在特殊的`exports`对象上增加函数和对象，来把函数和对象增加到模块的根作用域下。

本地变量在模块中是私有的，因为在Node.js中模块会被一个函数所包裹（详见[module wrapper](#module wrapper)）,上面的例子中，变量`PI`对于`circle.js`是私有的

如果你想要模块输出一个函数（类似构造函数）或者想要一次输出一个完整的对象而不是每次创建一个属性，就把它绑定在`module.exports`上，而不是`exports`。

下面， `bar.js`使用了`square`模块，而`square`模块则输出的是一个构造函数：

```
const square = require('./square.js');
var mySquare = square(2);
console.log(`The area of my square is ${mySquare.area()}`);
```

`square`模块定义在`square.js`中：

```
// 绑定在exports上不会修改当前模块，必须要使用module.exports 
module.exports = (width) => {
  return {
    area: () => width * width
  };
}
```

模块系统在`require（“module”）`模块中实现。


