---
title: 自己动手实现系列 ---- async、await
date: 2020-03-24 12:40:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- 自己动手实现系列
---
# 原理
就是利用 `generator` （生成器）分割代码片段。然后我们使用一个函数让其自迭代，每一个 `yield` 用 `promise` 包裹起来。执行下一步的时机由 `promise` 来控制

# 实现
``` js
function _asyncToGenerator(fn) {
  return function() {
    var self = this, args = arguments;
    return new Promise(function(resolve, reject) { // 将返回值 promise 化
      var gen = fn.apply(self, args); // 获取迭代器实例
      function _next(value) { // 执行下一步
        asyncGeneratorStep(gen, resolve, reject, _next, _throw, 'next', value);
      }
      function _throw(err) { // 抛出异常
        asyncGeneratorStep(gen, resolve, reject, _next, _throw, 'throw', err);
      }
      _next(undefined); // 第一次触发
    });
  };
}
```