---
title: 自己动手实现系列 ---- Array.prototype.map()
date: 2020-03-24 12:40:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- 自己动手实现系列
---
# 原理
先看看 `reduce` 和 `map` 的使用方法
``` js
let new_array = arr.map(function callback(currentValue[, index[,array) {/* Return element for new_array */ }[, thisArg])
let result = arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])
```

# 实现
第一种用 `for` 实现
``` js
Array.prototype.myMap = function(callback, thisArg) {
  let arr = [];
  for (let i = 0; i < this.length; i++) {
    arr.push(callback.call(thisArg, this[i], i, this));
  }
  return arr;
};
```
第二种用 `reduce` 实现
``` js
Array.prototype.myMap = function(callback, thisArg) {
  let result = this.reduce((accumulator, currentValue, index, array) => {
    accumulator.push(callback.call(thisArg, currentValue, index, array));
    return accumulator;
  }, []);
  return result;
};
```