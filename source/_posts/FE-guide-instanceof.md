---
title: 前端常见知识点整理 ---- instanceof
date: 2020-01-02 20:29:22
categories: 
	- 前端技术
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处


# `instanceof` 
`instanceof` 可以正确的判断对象的类型，因为内部机制是通过判断对象的原型链中是不是能找到类型的 `prototype。`

我们也可以试着实现一下 `instanceof`

```
// js代码

function instanceof(left, right) {
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