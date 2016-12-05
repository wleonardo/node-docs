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

因此Node.js寻找它加载的任何模块的真实路径（`relapath`），然后寻找模块的中`node_modules` 文件夹中的模块依赖关系，这种形式可以很简单的解决以下的文件结构：
* `/usr/lib/node/foo/1.2.3/` - 版本1.2.3的`foo`的内容。
* `/usr/lib/node/bar/4.3.2/` - `foo`依赖的`bar`包的内容
* `/usr/lib/node/foo/1.2.3/node_modules/bar` - 链接到的`/usr/lib/node/bar/4.3.2/`符号
* `/usr/lib/node/bar/4.3.2/node_modules/*` - `bar`包的所有依赖的链接符号

因此，即使遇到依赖形成循环，或者依赖有冲突，每个模块都可以获取到可以用的版本的依赖。

当`foo`包代码中有`require('bar')`，它会得到链接到`/usr/lib/node/foo/1.2.3/node_modules/bar`的版本。当`bar`包中引用了`require('quxux')`,它会得到链接到`/usr/lib/node/bar/4.3.2/node_modules/quux`的版本

此外，为了使模块查找的过程更加优化，我们可以吧包放在`/usr/lib/node_modules/<name>/<version>`中，而不是直接放在`/usr/lib/node`。这样Node.js就不用麻烦的在`/usr/node_modules`或者`/node_modules`寻找缺少的依赖。

为了使模块可以被Node.js的交互式解析器（REPL）使用。可以将`/usr/lib/node_modules`文件夹添加为`$NODE_PATH`环境变量。因此模块查找的`node_modules`都是相互关联的，`require()`都是基于文件的绝对地址来引用的，所以包可以放在任何地方。

## All Together
为了在`require()`的时候获取到精确的文件名字，会使用` require.resolve()`函数。

下面是`require.resolve()`的高级算法的伪码：
```
在路径为Y的模块中require(X)
1. 如果X为核心模块，
    a. 返回核心模块
    b. 停止
2. 如果X是以'./','/'或者'..'开头
    a. LOAD_AS_FILE(Y + X)
    b. LOAD_AS_DIRECTORY(Y + X)
3. LOAD_NODE_MODULES(X, dirname(Y))
4. 抛出 "not found"

LOAD_AS_FILE(X)
1. 如果X是一个文件，以Javascript文本的方式导入X。停止寻找
2. 如果X.js是一个文件，以Javascript文本的方式导入X.js。停止寻找
3. 如果x.json是一个文件，格式化X.json为一个对象。停止寻找
4. 如果X.node是一个文件，以二进制插件的方式导入X.node。停止寻找

LOAD_AS_DIRECTORY(X)
1. 如果X/package.json是一个文件
    a. 解析X/package.json ,然后寻找“main”字段
    b. let M = X + (json main field)
    c. LOAD_AS_FILE(M)
2. 如果X/index.js是一个文件，以Javascript文本的方式导入X/index.js。停止寻找
3. 如果X/index.json是一个文件，格式化X/index.json为一个对象。停止寻找
4. 如果X.node是一个文件，以二进制插件的方式导入X/index.node。停止寻找

LOAD_NODE_MODULES(X, START)
1. let DIRS=NODE_MODULES_PATHS(START)
2. for each DIR in DIRS:
   a. LOAD_AS_FILE(DIR/X)
   b. LOAD_AS_DIRECTORY(DIR/X)
   
NODE_MODULES_PATHS(START)
1. let PARTS = path split(START)
2. let I = count of PARTS - 1
3. let DIRS = []
4. while I >= 0,
   a. if PARTS[I] = "node_modules" CONTINUE
   c. DIR = path join(PARTS[0 .. I] + "node_modules")
   b. DIRS = DIRS + DIR
   c. let I = I - 1
5. return DIRS
```




