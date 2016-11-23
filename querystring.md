## 目录
* Query String
    * querystring.escape(str)
    * querystring.parse(str[, sep[, eq[, options]]])
    * querystring.stringify(obj[, sep[, eq[, options]]])
    * querystring.unescape(str)
    
#Query String
    稳定性：2 - 稳定
quertstring 模块提供了一系列用于解析和格式化url参数的工具，它可以被如下使用
```
const querystring = require('querystring');
```
## querystring.escape(str)
加入：v0.1.25
* `str` \<string>
`querystring.escape()` 可以对字符串进行百分比编码，同时针对url查询字符串的特定要求进行了优化

querystring.escape()被querystring.stringify()使用，但是一般不推荐直接使用。主要在应用程序代码在必要的时候被用来分配给替代函数来实现百分比编码 ？？

## querystring.parse(str[, sep[, eq[, options]]])
加入：v0.1.25
* `str` \<String> 待解析的url查询字符串
* `sep` \<String> 用于分界查询字符串中每对key与value的字符串，默认为`'&'`.
* `eq` \<String> 用于分界查询字符串中key与value的字符串，默认为`'='`.
* `options` \<Object>
    * `decodeURIComponent` \<Function>  设置一个函数解码百分比编码的查询字符串，默认使用`querystring.unescape()`.
    * `maxKeys` \<number>

  





