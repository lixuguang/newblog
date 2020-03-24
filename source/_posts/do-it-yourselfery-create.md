---
title: 自己动手实现系列 ---- Object.create()
date: 2020-03-24 12:40:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- 自己动手实现系列
---
# 实现一个 `Object.create()` 方法
1. 创建一个空匿名函数
2. 函数原型对象指向传入对象实例
3. 返回构造函数创建的实例

``` js
function create =  function (o) {
    var F = function () {};
    F.prototype = o;
    return new F();
};
```