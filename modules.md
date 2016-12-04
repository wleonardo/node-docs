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

## 访问main模块

当一个文件是直接被Node.js运行的，`require.main`被设置为它的`module`,这就是说你可以你可以通过以下的方式来判断一个文件是否是直接被Node.js运行的。

```
require.main === module
```

对于文件foo.js，如果是通过执行`node foo.js`来运行的，上面的测试就会返回true，而如果是通过`require('./foo')`的方式来运行的，则会返回false。

因为模块都有一个`filename`属性（一般等同于`__filename`）, 当前项目会通过核对`require.main.filename`来获得入口点。

## 另外：包管理器小提示
Node.js的`require()`函数的语义被设计为足够通用以来支持很多合理的目录结构。dpkg，rpm和npm等包管理系统希望能够在不修改的情况下从Node模块中构建本地包。

下面是一些我们我们建议的有效的目录结构：

我们希望保存一个包的一个特定版本的内容下`/usr/lib/node/<some-package>/<some-version>`的文件夹中。

包可能依赖于其他的包。为了安装一个`foo`包，我们可能需要安装一个特定版本`bar`包。在某些情况下，`bar`可能还有他自己的依赖。这些依赖性甚至可能碰撞或形成循环。




