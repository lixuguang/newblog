---
title: 自己动手实现系列 ---- call、apply、bind
date: 2020-03-24 23:54:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- 自己动手实现系列
---
# 原版
先来看一个call实例，看看call到底做了什么：
``` js
let foo = {
  value: 1
};
function bar() {
  console.log(this.value);
}
bar.call(foo); // 1
```
从代码的执行结果，我们可以看到，call首先改变了this的指向，使函数的this指向了foo,然后使bar函数执行了。
总结一下：
- call改变函数this指向
- 调用函数

# 自己动手
1. 首先我们对参数 `context` 做了兼容处理，不传值， `context` 默认值为 `window` ；
2. 然后我们将函数挂载到 `context` 上面, `context.fn = this` ；
3. 处理参数，将传入 `myCall` 的参数截取，去除第一位，然后转为数组；
4. 调用 `context.fn` ，此时 `fn` 的 `this` 指向 `context` ；
5. 删除对象上的属性 `delete context.fn` ；
6. 将结果返回。

``` js
Function.prototype.myCall = function(context) {
  context = context || window;
  context.fn = this; // 将函数挂载到对象的fn属性上
  const args = [...arguments].slice(1); // 处理传入的参数
  const result = context.fn(...args); // 通过对象的属性调用该方法
  delete context.fn; // 删除该属性
  return result // 返回结果
};
```
`apply` 跟 `call` 的区别在于参数， 其他没有差别，实现如下
``` js
// myApply的参数形式为(obj,[arg1,arg2,arg3]);
// 所以myApply的第二个参数为[arg1,arg2,arg3]
// 这里我们用扩展运算符来处理一下参数的传入方式
Function.prototype.myApply = function(context) {
  context = context || window
  context.fn = this
  let result
  if (arguments[1]) { // 判断是否有第二个参数
    result = context.fn(…arguments[1]) // 有的话传入执行
  } else {
    result = context.fn() // 没有的话空参执行
  }
  delete context.fn;
  return result
};
```
`bind` 和 `call` 、 `apply` 作用都是改变 `this` 的指向，区别在于 `bind` 改变后不会立即执行，而 `call` 和 `apply` 会立即执行，我们看一下 `bind` 的用法
``` js
function Person(){
  this.name="zs";
  this.age=18;
  this.gender="男"
}
let obj={
  hobby:"看书"
}

let changePerson = Person.bind(obj); // 将构造函数的this绑定为obj
changePerson(); // 直接调用构造函数,函数会操作obj对象,给其添加三个属性;
console.log(obj); // => {hobby:"看书",name:"zs",age:18,gender:'男'}
let p = new changePerson(); // 用改变了this 指向的构造函数,new一个实例出来
console.log(p); // => Person {name:"zs",age:18,gender:'男'}
```
仔细观察上面的代码，再看输出结果。

我们对 `Person` 类使用了 `bind` 将其 `this` 指向 `obj` ，得到了 `changePerson` 函数，此处如果我们直接调用 `changeperson` 会改变 `obj` ，若用 `new` 调用 `changeperson` 会得到实例 `p`，并且其 `__proto__` 指向 `Person` ,我们发现 `bind` 失效了。

我们得到结论：***用 `bind` 改变了 `this` 指向的函数，如果用 `new` 操作符来调用， `bind` 将会失效***。

这个对象就是这个构造函数的实例，那么只要在函数内部执行 *** this instanceof 构造函数 *** 来判断其结果是否为 `true` ，就能判断函数是否是通过 `new` 操作符来调用了，若结果为 `true` 则是用 `new` 操作符调用的，总结如下：
1. 保存当前 `this` 指向
2. 保存环境上下文
3. 保存参数，去掉第一个对象参数
4. 返回待执行函数
  1. 数组化剩余参数
  2. 判断是否为构造函数
  3. 若是执行构造函数，若不是改变 `this` 指向执行
``` js
// bind实现

Function.prototype.myBind = function(context){
  let _this = this; // 1、保存函数
  context = context || window; // 2、保存目标对象
  let rest = [...arguments].slice(1); // 3、保存目标对象之外的参数,将其转化为数组;
  // 此处开始与 call 和 apply 不同，不是返回结果，而是返回一个函数
  return function F(){ // 4、返回一个待执行的函数
    let rest2 = Array.prototype.slice.call(arguments) // 5、这里的arguments是F函数的参数，转换为数组;
    if(this instanceof F){
      return new _this(...rest2) // 6、若是用new操作符调用,则直接用new 调用原函数,并用扩展运算符传递参数
    }else{
      _this.apply(context,rest.concat(rest2));  // 7、用apply调用第一步保存的函数，并绑定this，传递合并的参数数组，
                                                // 即context._this(rest.concat(rest2))
    }
  }
};
```