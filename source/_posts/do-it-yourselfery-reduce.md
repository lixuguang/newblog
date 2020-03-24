---
title: 自己动手实现系列 ---- Array.prototype.reduce
date: 2020-03-24 12:40:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- 自己动手实现系列
---
# 原版
``` js
Array.prototype.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])
```

# 自己动手
``` js
Array.prototype.myReduce = function(callback, initialValue) {
  let accumulator = initialValue ? initialValue : this[0]; // 第一次使用判断时候有initialValue参数，如果有用他，没有用this[0]，这里this指的是传入的数组，this[0]就是数组第一项
  for (let i = initialValue ? 0 : 1; i < this.length; i++) { // 如果有初始值从0开始循环，不然从1开始
    let _this = this; // 保留当前this指向
    accumulator = callback(accumulator, this[i], i, _this); // 
  }
  return accumulator; // 返回迭代器的终值
};
```
试用一下
``` js
let arr = [1, 2, 3, 4];
let sum = arr.myReduce((acc, val) => {
  acc += val;
  return acc;
}, 5);

console.log(sum); // 15
```