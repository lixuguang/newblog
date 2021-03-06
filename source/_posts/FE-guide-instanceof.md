---
title: 前端常见知识点整理 ---- instanceof
date: 2020-01-02 20:29:22
categories: 
	- 前端技术
	- 前端常见知识点整理
tags: 
	- 面试
---
> 作者：李旭光
> 引用请标明出处


# `instanceof` 
`instanceof` 可以正确的判断对象的类型，因为内部机制是通过判断对象的原型链中是不是能找到类型的 `prototype`。
举例：
``` js
a instanceof Object
```
判断 `Object` 的 `prototype` 是否在 `a` 的原型链上。

我们也可以试着实现一下 `instanceof`

``` js
function myInstanceof(left, right) { // left 表示左表达式，right 表示右表达式
    let prototype = right.prototype // 获得类型的原型
    left = left.__proto__ // 获得对象的原型
		
    while (true) { // 判断对象的类型是否等于类型的原型
    	if (left === null)
    		return false
    	if (prototype === left)
    		return true
    	left = left.__proto__
    }
}
```