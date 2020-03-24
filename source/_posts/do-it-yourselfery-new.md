---
title: 自己动手实现系列 ---- new
date: 2020-03-24 12:40:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- 自己动手实现系列
---
# 实现一个new操作符
我们首先知道new做了什么：

1. 创建一个空的简单 `JavaScript` 对象（即{}）
2. 链接该对象（即设置该对象的构造函数）到另一个对象
3. 将步骤（1）新创建的对象作为 `this` 的上下文
4. 如果该函数没有返回对象，则返回 `this`

知道new做了什么，接下来我们就来实现它
``` js
function create(){
  // 创建一个空的对象
  let obj = {};
	// 获得构造函数
    let Con = [].shift.call(arguments)
  // 将空对象指向构造函数的原型链
  Object.setPrototypeOf(obj, Con.prototype);
	// obj.__proto__ = Con.prototype // 链接到原型
  // obj绑定到构造函数上，便可以访问构造函数中的属性，即this.obj.Con(args)
  let result = Con.apply(obj, arguments);
  // 如果返回的result是一个对象则返回
  // new方法失效，否则返回obj
  return result instanceof Object ? result : this.obj;
	// return typeof result === 'object' ? result : obj// 确保 new 出来的是个对象
}
```