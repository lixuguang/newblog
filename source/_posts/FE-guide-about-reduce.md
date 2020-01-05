---
title: reduce函数的妙用 ---- 实现map和filter
date: 2020-01-05 16:55:00
categories: 
	- 前端技术
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处


# 前言
最近看了好多技术文章，好多的代码片段，突然发现好多的代码里都有 `reduce` ，感觉以前都没怎么关注过这个函数，为了弥补这个过失，打算专门写一篇文章说说它。

`reduce` 函数在 MDN 中是这样介绍的
> reduce() 方法对数组中的***每个元素***执行一个由 ***您*** 提供的 `reducer` 函数(***升序执行***)，将其结果汇总为单个返回值。

说实话看了一脸懵逼，这上面说的叫人话？后来耐着性子看了一些代码后感觉有所理解，如果理解不对，还请斧正。
首先看一下这里面几个关键词

*** 每个元素： *** 这就是遍历咯，没啥好说的
***您***提供的 `reducer` 函数：我哪有什么 `reducer` 函数呢，后来理解了，就是回调，有的地方呢叫 `handler` ，这里跟 `reduce` 配合就叫了 `reducer`。
***升序执行***：就是说是0，1，2下标这样的顺序执行啦。
将其结果汇总为单个返回值：最后返回的是一个值，当然没说必须是什么类型。

这样解释完了以后，上面这句话就比较好懂了，简单说就是 `reduce` 里传一个回调函数，执行回调函数方法后返回一个值，就是这样。

> reducer 函数接收4个参数:
- Accumulator (acc) (累计器)
- Current Value (cur) (当前值)
- Current Index (idx) (当前索引)
- Source Array (src) (源数组)
您的 `reducer` 函数的返回值分配给累计器，该返回值在数组的每个迭代中被记住，并最后成为最终的单个结果值。

看上面的描述一定要注意，这些参数是 `reducer` 的参数，不是 `reduce` 的参数，一共有4个，通常用前两个情况比较多。

那么 `reduce` 函数呢实际上有两个参数，第二个还可以省略 `reducer` 和 `initialValue`，`initialValue`作为第一次调用 `reducer` 函数时的第一个参数的值。 如果没有提供初始值，则将使用数组中的第一个元素。 ***在没有初始值的空数组上调用 `reduce` 将报错***，这点切记。

终于把前置知识讲清楚了，接下来，我们就用 `reduce` 搞点事情。

## 1. 使用 `reduce` 实现 `map`
`map` 的话我不想再讲一遍，一句话带过，就是对回调函数中的元素进行加工后返回一个长度一样的新数组。
那么该如何实现呢，我们来想想原理吧，刚才上面说过了，reduce执行过后的返回值会作为下一次执行的第一个参数放进去，那么就可以先用一个空数组作为初始值来接受每次需要处理的元素的集合，并返回回去，作为下一次传入的第一个参数，这样每次操作完之后就可以push到这个数组中，那么剩下要做的就是处理数组中每一项的函数了，我们叫他 `handler`
```
// js代码

Array.prototype.reduceToMap = function (handler) { // 自定义 `map` 函数 `reduceToMap`
  return this.reduce((target, current, index) => { // this指向调用他的数组
    target.push(handler.call(this, current, index)) // 这里用了call方法，handler将接受两个参数 current和index
    return target; // 处理完成后返回新数组
  }, []) // 初始化空的新数组
};

```
就这样我们就用 `reduce`，实现了 `map` 的功能，是不是很好用？

## 2. 使用 `reduce` 实现 `filter`
`filter` 也是数组常用的方法，同样传入一个回调函数，处理结果返回true或false，最终 `filter` 会返回一个过滤后的函数。
学会了上面的 `map` 的实现，实际上 `filter` 就会很简单
```
// js代码

Array.prototype.reduceToFilter = function (handler) { // 还是自定义方法名
  return this.reduce((target, current, index) => {
    if (handler.call(this, current, index)) { // 这里注意 handler 要返回的是布尔类型的值
      target.push(current); // 符合条件就插入新数组
    } // 不符合就什么都不做
    return target; // 最后返回新数组
  }, []) // 初始化一个空数组
};
```
日后在看到 `reduce` 的妙用之后还会来补充这篇文章，如果实在懒得写，我也会链接一下新文章，希望大家长期关注。