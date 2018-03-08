业务代码写的多了，就会有更进一步的想法。比如在工作里经常使用的别的设计的优秀的库，如jQuery，zeptojs，lodashjs等等，之前更多的是拿来就用，现在想熟悉下它们的设计思想，因为 zeptojs 是个设计优秀，体积小的库，而且用的也比较多，第一次阅读源码就从它开始了。

下面开始正文。

## 整体结构

我们把源码（[zepto.js v1.2.0](http://zeptojs.com/) ）放进带有代码缩进功能的编辑器里，经过折叠，得到了如下的整体结构图。

![zepto](/images/structure.png)

继续整理，

```js
var Zepto = (function () {...})()

window.Zepto = zepto;
// 如果$变量符号未被使用，则把zepto对象传递给它
window.$ === undefined && (window.$ = Zepto)

return Zepto
```

Zepto 源码它是一个立即执行函数（IIFE），内部设计了各种方法和逻辑，最后暴露给全局变量`zepto`，如果变量`$`还未定义，也把`zepto`赋值给`$`。

## 核心代码

继续看一些`zepto`里的核心代码，同样我们先不去分析它里面的各种方法，先把整个流程走下来。

```js
var $, zepto = {};

function Z(dom, selector) {
  var i,
    len = dom ? dom.length : 0
  for (i = 0; i < len; i++) this[i] = dom[i]
  this.length = len
  this.selector = selector || ''
}

zepto.Z = function(dom, selector) {
  return new Z(dom, selector)
}

zepto.isZ = function(object) {
  return object instanceof zepto.Z
}

zepto.init = function(selector, context) {
  var dom
  ...
  return zepto.Z(dom, selector)
}

$ = function(selector, context) {
  return zepto.init(selector, context)
}

$.fn = {
  constructor: zepto.Z,
  length: 0,
  slice: function(){
    ...
    return this;   
  },
  ...
}

zepto.Z.prototype = Z.prototype = $.fn

$.zepto = zepto

return $
```

先说`$`，$ 是一个函数，该函数调用了 zepto.init() 方法，该方法会获取到`DOM`元素集合，经由`zepto.Z`方法处理，返回一个`Z`的实例。同时`$` 上面还绑定了各种属性和方法（主要在`$.fn`下面）。

`$`函数会返回一个`Z`的实例，但是该实例又是怎么拥有`$`的各种方法呢，原因也很简单，就在于：  
*zepto.Z.prototype = Z.prototype = $.fn*

`Z`的实例会继承`$.fn`的各种方法。这才把 `$`和`zepto`联系在了一起。




