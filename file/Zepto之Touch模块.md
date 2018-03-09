# Zepto 之 Touch 模块源码学习

先说一下，官网下载的 `Zeptojs` 包含了绝大部分的功能，代码不再兼容IE低版本浏览器，体积也轻巧。同时，`Zeptojs` 在主代码库之外还封装了很多模块，例如Touch 模块、IE 模块、ios3模块等等。

## 为什么要引入Touch模块，它实现了那些功能

众所周知，`click` 事件在移动端会有 300ms 的延迟，并且在移动端有很丰富的交互，比如“长按”，“左划”等。所以`Zeptojs`在这个库实现了'swipe', 'swipeLeft', 'swipeRight', 'swipeUp', 'swipeDown', 'doubleTap', 'tap', 'singleTap', 'longTap' 这样一些功能。

```js
;['swipe', 'swipeLeft', 'swipeRight', 'swipeUp', 'swipeDown',
  'doubleTap', 'tap', 'singleTap', 'longTap'].forEach(function(eventName){
  $.fn[eventName] = function(callback){ return this.on(eventName, callback) }
})
```
`Zeptojs` 注册的所有事件。

依次为：滑动事件，左滑、右滑、上滑、下滑、屏幕双击、屏幕点击、单击事件、长按事件。

我们需要先了解以下四种 `touch` 事件来了解基本的 touch 事件。  

- touchstart：触摸屏幕的时候触发
- touchmove：手指在屏幕上移动的时候触发
- touchend：手指离开屏幕的时候触发
- touchcancel：系统取消 touch 事件的时候触发

另外知道 `pageX` 和 `pageY` 是 touch 模块将要用到计算的一个属性，描述了触摸点相对于页面的位置。

## 主要结构

```js
function setup(__eventMap) {
  var now,
    delta,
    deltaX = 0,
    deltaY = 0,
    firstTouch,
    _isPointerType
  ...
}

$(document).ready(setup)
```
这里 `now` 用来存当前时间，`delta` 用来存两次触摸时的时间差；`deltaX` 存两次触摸后 X 轴上的位移，`deltaY` 存两次触摸后 Y 轴上的位移...

### 主要方法

#### swipeDirection 计算方向
```js
function swipeDirection(x1, x2, y1, y2) {
  return Math.abs(x1 - x2) >=
    Math.abs(y1 - y2) ? (x1 - x2 > 0 ? 'Left' : 'Right') : (y1 - y2 > 0 ? 'Up' : 'Down')
}
```

#### longTap 和 cancelLongTap
```js
var touch = {},
  touchTimeout, tapTimeout, swipeTimeout,longTapTimeout,
  longTapDelay = 750,
  gesture,
  down, up, move,
  eventMap,
  initialized = false

function longTap() {
  longTapTimeout = null
  if (touch.last) {
    touch.el.trigger('longTap')
    touch = {}
  }
}

function cancelLongTap() {
  if (longTapTimeout) clearTimeout(longTapTimeout)
  longTapTimeout = null
}
```
在触发 `longTap` 事件前，先把 `longTapTimeout` （`longTapTimeout = setTimeout(longTap, longTapDelay)` 保存的一个定时器）清空，如果有`touch.last`，就触发`longTap` 事件。

`cancelLongTap` 取消长按事件的触发。

更多可以参见代码，写的逻辑也比较清晰，代码设计的也很好读。
