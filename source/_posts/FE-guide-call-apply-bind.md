---
title: 前端常见知识点整理 ---- call, apply, bind 区别
date: 2020-01-03 10:19:00
categories: 
  - 前端技术
  - 前端常见知识点整理
tags: 
	- 面试
---
> 作者：李旭光
> 引用请标明出处


# call, apply, bind 区别
首先说下前两者的异同。
相同： `call` 和 `apply` 都是为了解决改变 `this` 的指向。
不同：传参的方式不同，除了第一个参数外， `call` 可以接收一个参数***列表***， `apply` 只接受一个参数***数组***。
```
// js代码
let anObj = {
    value: 1
}
function getValue(name, age) {
    console.log(name)
    console.log(age)
    console.log(this.value)
}
getValue.call(anObj, 'lixuguang', '31')
getValue.apply(anObj, ['lixuguang', '31'])
```
## 模拟实现 `call` 和 `apply`

可以从以下几点来考虑如何实现
- 不传入第一个参数，那么默认为 `window`
- 改变了 `this` 指向，让新的对象可以执行该函数。那么思路是否可以变成给新的对象添加一个函数，然后在执行完以后删除？
```
// js代码

Function.prototype.myCall = function (context) {
  var context = context || window // 有入参用入参，没有给 window
  context.fn = this  // 给 context 添加一个属性，getValue.call(anObj, 'lixuguang', '31') => anObj.fn = getValue
  var args = [...arguments].slice(1) // 将 context 后面的参数取出来
  var result = context.fn(...args) // getValue.call(anObj, 'lixuguang', '31') => anObj.fn('yck', '24')
  delete context.fn // 删除 fn
  return result
}
```
以上就是 call 的思路，apply 的实现也类似
```
// js代码

Function.prototype.Apply = function (context) {
  var context = context || window // 有入参用入参，没有给 window
  context.fn = this  // 给 context 添加一个属性，getValue.call(anObj, 'lixuguang', '31') => anObj.fn = getValue
  var args = arguments[1] // 将 context 后面的参数取出来
  var result = args?context.fn(...args):context.fn() // getValue.call(anObj, ['lixuguang', '31']) => anObj.fn('yck', '24')
  delete context.fn // 删除 fn
  return result
}
```
`bind` 和其他两个方法作用也是一致的，只是该方法会返回一个函数。并且我们可以通过 `bind` 实现柯里化。

同样的，也来模拟实现下 `bind`
```
// js代码

Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  var _this = this
  var args = [...arguments].slice(1)
  
  return function F() { // 返回一个函数，这是 `bind` 和 `call` ， `apply` 的区别
    if (this instanceof F) { // 因为返回了一个函数，我们可以 new F()，所以需要判断
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}
```