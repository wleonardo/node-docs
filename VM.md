###目录
* 运行 Javascript
    * class vm.Script
    * new vm.Script(code, options)
    * script.runInContext(contextifiedSandbox[, options])
*


## 运行Javascript
    稳定性：2 - 稳定版（Stable）

  vm模块提供了让代码在V8虚拟机环境中编译和运行的接口，它可以被如下方式所访问使用：
```
const vm = require('vm');
```
### class vm.Script
###### v0.3.1被加入
* `code` \<string> 需要被编译的Javascript代码 
* `options`
    * `filename` \<string> 
指定由此脚本生成的堆栈跟踪中使用的文件名
    *  `lineOffset` \<number> 指定由此脚本生成的堆栈跟踪中显示的偏移行数 
    *   `columnOffset` \<number> 指定由此脚本生成的堆栈跟踪中显示的偏移列数
    *   `displayErrors` \<boolean> 参数为true时，当代码编译时发生错误时，引起错误的代码的行数会被附加到堆栈跟踪中
    *   `timeout` \<number> 指定中断执行前代码执行的时间，如果执行被中断，就会抛出一个错误
    *   `cachedData` \<Buffer>



