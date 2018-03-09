`js`里面最常操作的的基本数据类型就是字符串了，`zeptojs` 内部也定义了许多有用的方法，还有数组等相关的一些方法，这里稍微整理了一下。

## 字符串

### `camelize`
```js
var camelize = function(str) {
  return str.replace(/-+(.)?/g, function(match, chr) {
    return chr ? chr.toUpperCase() : ''
  })
}
```
作用是将以中划线（-）分割的字符串转化成驼峰风格的字符串，譬如`char-char`到`charChar`的转变。

### `dasherize`

```js
function dasherize(str) {
  return str
    .replace(/::/g, '/')
    .replace(/([A-Z]+)([A-Z][a-z])/g, '$1_$2')
    .replace(/([a-z\d])([A-Z])/g, '$1_$2')
    .replace(/_/g, '-')
    .toLowerCase()
}
```
与`camelize`相反的是，它将驼峰式风格的字符串转换成中划线连接的字符串。  
第一次：将全局的`/::/`转换成`/`，  
第二次：将出现一次或多次大写字母的区间，与出现一次大写字母紧接着小写字母的区间加上`_`，比如`ABCDream`->`ABC-Dream`。  
第三次：将出现一个小写字母或数字的区间，与出现一个大写字母的区间之间加上`_`，比如`a1B`->`a1_B`。  
第四次：将所有的`_`转换成`-`。  
最后：将所有的大写字母转换成小写形式。  

## 数组

`zeptojs` 在一开始是这样定义的，把数组原型上的一些方法赋值给某个变量。这样在`js`  里也是很常见的，主要是为了简化代码，方便书写，减小最后代码体积...  
```js
var emptyArray = [],
  concat = emptyArray.concat,
  filter = emptyArray.filter,
  slice = emptyArray.slice,
```

### `compact`

```js
function compact(array) {
  return filter.call(array, function(item) {
    return item != null
  })
}
```
因为`null == undefined`，所以该方法可以把数组中的`null`，`undefined`移除。

### `flatten`

```js
function flatten(array) {
  return array.length > 0 ? $.fn.concat.apply([], array) : array
}
```
记得`lodashjs`中也有这个方法，扁平化数组（二维，如果是多维数组则无效）。  
`concat()` 方法将数组和/或值连接成新数组，并返回一个新数组。比如：  
```js
var newArr = [].concat('a', 4, [5,6]); // ['a', 4, 5, 6]
```

### `uniq`

```js
var uniq = function(array) {
  return filter.call(array, function(item, idx) {
    return array.indexOf(item) == idx
  })
}
```
用于数组去重。

这个涉及到的知识点有：`filter`方法并不会改变原函数。方法会遍历整个数组，然后检查每个元素第一次出现的位置，是否和当前元素的索引一致。不一致则说明重复了，不予返回。

## 类型判断

### 初始化定义

```js
var class2type = {},
  toString = class2type.toString

// 生成（填充）class2type
$.each('Boolean Number String Function Array Date RegExp Object Error'.split(' '), function(i, name) {
    class2type['[object ' + name + ']'] = name.toLowerCase()
  }
)
/*
class2type = {
  "[object Boolean]": "boolean",
  "[object String]": "string",
  ...
}
*/


function type(obj) {
  return obj == null
    ? String(obj)
    : class2type[toString.call(obj)] || 'object'
}
```
`type` 方法用于返回该数据的数据类型。

如果是 `null` 或 `undefined`，则会返回字符串`null`或 `undefined`。  
如果不是上面二者，则会先调用 `toString.call(obj)` 方法，也即 `Object.prototype.toString.call(obj)`，结果返回一个字符串，类似 `"[object String]"`这样，再调用 `class2type` 取出对应值 `string`。如果找不到对应的结果，则返回 `object`。

**isWindow**  
```js
function isWindow(obj) {
  return obj != null && obj == obj.window
}
```
用于检查是否是浏览器内的 `window` 对象。

该对象不为 `null` 或 `undefined`，且 `obj.window` 等于自身的引用。

**isDocument**  
```js
function isDocument(obj) {
  return obj != null && obj.nodeType == obj.DOCUMENT_NODE
}
```
用于判断是否为 `document` 对象。

每个DOM节点都有 `nodeType` 属性，每个属性值都有对应的常量，`document` 的 `nodeType` 值为 9，为常量 `DOCUMENT_NODE`。见[nodeType](https://developer.mozilla.org/en-US/docs/Web/API/Node/nodeType)

**isArray**
```js
var isArray = Array.isArray || function(object) {
  return object instanceof Array
}
```
用来判断是否是数组。

如果浏览器支持原生的 `isArray` 方法，就用原生；如果不支持，就判断是否是 `Array` 的实例。

**likeArray**
```js
function likeArray(obj) {
  var length = !!obj && 'length' in obj && obj.length,
    type = $.type(obj)

  return (
    'function' != type &&
    !isWindow(obj) &&
    ('array' == type ||
      length === 0 ||
      (typeof length == 'number' && length > 0 && length - 1 in obj))
  )
}
```
用来判断是否是类数组数据。

什么样的对象是类数组？见[like-Array](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Indexed_collections)

函数内部的 `arguments` 对象就是最常见的类数组对象， `arguments` 对象提供了一个 `length`  属性，这种并不是数组，但是又可以通过某些方式利用数组的方法，来方便我们进行一些计算。
```js
function func(a,b,c) {
  Array.prototype.forEach.call(arguments, function(item) {
    console.log(item);
  });
}
func(1,2,3) // 1,2,3
```

## 参考
- [JavaScript 中 apply 、call 的详解--issue](https://github.com/lin-xin/blog/issues/7)
- [String.prototype.replace()--MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)
- [flatten--lodash中文文档](https://www.lodashjs.com/docs/4.17.5.html#flatten)