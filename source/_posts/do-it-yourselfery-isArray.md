---
title: 自己动手实现系列 ---- Array.isArray
date: 2020-03-24 12:40:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- 自己动手实现系列
---
# 实现一个Array.isArray
思路很简单，就是利用 `Object.prototype.toString`

``` js
Array.myIsArray = function(o) { 
  return Object.prototype.toString.call(Object(o)) === '[object Array]'; 
};
```