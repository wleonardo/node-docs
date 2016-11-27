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
    * `maxKeys` \<number> 指定编码时key数量最多为多少。默认是1000，设置为0则表示不限数量
`querystring.parse()`函数可以解析一个url的查询字符串（`str`）为一个键值对（key：value）的集合
例如，一个查询字符串为`'foo=bar&abc=xyz&abc=123'`可以被解析为
```
{
    foo: 'bar',
    abc: ['xyz', '123']
}
```
注：`querystring.parse()`返回的对象不是原型继承于Javascript的`Object`对象，所以类似`obj.toString()`, `obj.hasOwnProperty()`等典型的`Object`对象的方法将无法使用

默认情况下，百分比编码查询字符串时会使用UFT-8编码，如果使用代替字符编码，则需要指定代替字符的`decodeURIComponent`选项，如下例子
```
// 假定 gbkDecodeURIComponent 函数已经存在..
querystring.parse('w=%D6%D0%CE%C4&foo=bar', null, null, { decodeURIComponent: gbkDecodeURIComponent })
```
##querystring.stringify(obj[, sep[, eq[, options]]])
 加入：v0.1.25
 * `obj` \<Object> 要被序列化为查询字符串的对象.
 * `sep` \<String> 用于分界查询字符串中每对key与value的字符串，默认为`'&'`.
* `eq` \<String> 用于分界查询字符串中key与value的字符串，默认为`'='`.
* `options`
    * `encodeURIComponent` \<Function> 在转换查询字符传时把url中不安全的的字符串转换为百分比编码的函数.默认使用`querystring.escape()`.
`querystring.stringify()`函数是通过遍历对象的“own properties“来生成url查询字符串
例子：
```
querystring.stringify({ foo: 'bar', baz: ['qux', 'quux'], corge: '' })
// returns 'foo=bar&baz=qux&baz=quux&corge='

querystring.stringify({foo: 'bar', baz: 'qux'}, ';', ':')
// returns 'foo:bar;baz:qux'
```
一般，查询字符串中需要百分比编码的字符会被编码为UTF-8格式。如果需要代替的编码，那么将需要指定另一个encodeURIComponent选项，如以下示例所示
```
// 假定 gbkEncodeURIComponent 函数已经存在,

querystring.stringify({ w: '中文', foo: 'bar' }, null, null,
  { encodeURIComponent: gbkEncodeURIComponent })
```
##querystring.unescape(str)
加入：v.0.1.25
* `str` \<String>
`querystring.unescape()`方法可以对提供的字符串进行url的百分比编码.
The querystring.unescape() method is used by querystring.parse() and is generally not expected to be used directly. It is exported primarily to allow application code to provide a replacement decoding implementation if necessary by assigning querystring.unescape to an alternative function
`querystring.unescape()`方法被`querystring.parse()`函数所使用，一般来说也不推荐直接使用该函数。主要在应用程序代码在必要的时候被用来分配给替代函数来实现替换解码？？
默认情况下，`querystring.unescape()`函数会尝试使用基于`decodeURIComponent()`方法来进行解码，这样即使解码失败了，也会输出一个更加安全等效的结果，而不会输出一个错误的url


  





