`Zepto` 内部本身就提供了很多的工具函数，下面可以简单了解下。

## $.camelCase
```js
// 该方法会调用之前定义的 camelize 方法
$.camelCase = camelize
```

## $.contains 
```js
$.contains = document.documentElement.contains
  ? function(parent, node) {
      return parent !== node && parent.contains(node)
    }
  : function(parent, node) {
      while (node && (node = node.parentNode))
        if (node === parent) return true
      return false
    }
```
用来检查给定的父节点中是否包含有给定的子节点。
第一步先检查浏览器是否支持 `contains` 方法，若支持，则直接调用 `contains` 方法，否则开始执行另一个匿名函数，循环遍历寻找子节点的父节点，如果找到返回 `true`,否则返回 `false`。


## $.each
```js
$.each = function(elements, callback) {
  var i, key
  if (likeArray(elements)) {
    for (i = 0; i < elements.length; i++)
      if (callback.call(elements[i], i, elements[i]) === false)
        return elements
  } else {
    for (key in elements)
      if (callback.call(elements[key], key, elements[key]) === false)
        return elements
  }

  return elements
}
```
`$.each` 接受两个参数，获取到的`nodelist`元素集合，以及回调函数。  

如果该元素集合为 `Like-Array` 的就用for循环比那里绑定；如果为其他对象，则就用 `for...in...` 遍历对象的属性，并将属性和属性值传递给回调函数。值得一提的是，回调函数绑定 `this` 的时候，都用了 `call`，很方便的把上下文环境给了当前元素。

## $.grep
```js
$.grep = function(elements, callback) {
  return filter.call(elements, callback)
}
```
grep方法其实就是数组的 `filter` 方法。

## $.inArray
```js
$.inArray = function(elem, array, i) {
  return emptyArray.indexOf.call(array, elem, i)
}
```
返回元素在数组中的索引值。

第一个参数为查找函数，第二个为要查找的元素，第三个为从哪个位置开始查找。

## $.isArray
```js
$.isArray = isArray
```
直接调用了内置的 `isArray` 方法。

## $.trim
```js
$.trim = function(str) {
  return str == null ? '' : String.prototype.trim.call(str)
}
```
去除字符串两端空格。直接调用了字符串原生的 `trim` 方法。

。。。
