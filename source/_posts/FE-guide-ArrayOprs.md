---
title: 数组常见操作 ---- 去重、扁平、取最大最小值
date: 2020-01-05 11:05:00
categories: 
	- 前端技术
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处


# 去重
## 1. 利用 `Object` 的 `Key` 唯一特性
开辟一个外部存储空间用于标示元素是否出现过。
```
// js代码

const unique = (array)=> {
    var container = {};
    return array.filter((item, index) =>  container.hasOwnProperty(item) ? false : (container[item] = true));
}

```

## 2. 利用 `indexOf` 的返回值数值进行去重
原理是 `indexOf` 获取元素时如果返回值不等于下标说明已经有了，配合 `filter` 更美味
```
// js代码

const unique = arr => arr.filter((e,i) => 
  arr.indexOf(e) === i // 如果元素找到的当前下标和当前索引相同说明是同一个，不同说明不是唯一
);
```

还有一种变形方法利用 `lastIndexOf` 方法
> lastIndexOf() 方法返回指定元素（也即有效的 JavaScript 值或变量）在数组中的最后一个的索引，如果不存在则返回 -1。从数组的后面向前查找，从 fromIndex 处开始。

```
// js代码

const filterNonUnique = arr => arr.filter(e => 
  arr.indexOf(e) === arr.lastIndexOf(e) // 判断一个元素出现时的第一次下标和最后一次下标是否相同，如果相同那么就唯一
)
```
## 3. 利用 `Set` 特性去重
`Set` 是 `ES6` 中新的数据类型，它的特点就是元素唯一性，且可以和数组进行转换
```
// js代码

const unique = arr => Array.from(new Set(arr)); // Array.from 方法从一个类似数组或可迭代对象创建一个新的，浅拷贝的数组实例。

// 优化

const unique = arr => [...new Set(arr)]; // 利用 `ES6` 中展开操作

```
## 4. 排序后判断前后两项是否相等去重
通过比较相邻数字是否重复，将排序后的数组进行去重。
```
// js代码

const unique = (array) => {
  array.sort((a, b) => a - b);
  let pre = 0;
  const result = [];
  for (let i = 0; i < array.length; i++) {
    if (!i || array[i] != array[pre]) {
      result.push(array[i]);
    }
    pre = i;
  }
  return result;
}
```

# 扁平
## 1. 普通方法
通过递归的方式判断数组中的项是否是数组，如果不是就加入到新的扁平数组，如果是就递归调用逐层判断，直到全部结束
```
// js代码

const flatten = (array) => { // array 原数组
  let result = []; // 定义新的扁平数组
  for (let i = 0; i < array.length; i++) {
    if (Array.isArray(array[i])) { // 判断子元素是否是数组
      result = result.concat(flatten(array[i])); // 递归判断
    } else {
      result.push(array[i]); // 加入新数组
    }
  }
  return result;
}
```

## 2. 使用reduce简化上述方法
> reduce() 方法对数组中的每个元素执行一个由您提供的reducer函数(升序执行)，将其结果汇总为单个返回值。
> reducer 函数接收4个参数:
- Accumulator (acc) (累计器)
- Current Value (cur) (当前值)
- Current Index (idx) (当前索引)
- Source Array (src) (源数组)
- 您的 reducer 函数的返回值分配给累计器，该返回值在数组的每个迭代中被记住，并最后成为最终的单个结果值。

先看一段 reduce 的示例函数
```
// js代码

const array1 = [1, 2, 3, 4];
const reducer = (accumulator, currentValue) => accumulator + currentValue; // 定义一个累计器函数，作用是将数组前后累计值与当前值累加

// 1 + 2 + 3 + 4
console.log(array1.reduce(reducer)); // 没有初始值用第一个元素
// expected output: 10

// 5 + 1 + 2 + 3 + 4
console.log(array1.reduce(reducer, 5)); // 有初始值从初始值开始
// expected output: 15

```
这下大家应该对 reduce 函数认识了，接下来看看怎么简化
```
// js代码

function flatten(array) {
  return array.reduce((newArray, current) => // 新数组，当前项
    Array.isArray(current) ? // 判断当前项是否为数组
      newArray.concat(flatten(current)) : // 是的话 递归调用
      newArray.concat(current) // 不是的话加进新数组
  , []) // 初始化新数组为空
}
```
这里我们再变一个形，增加一个变量，变成可指定深度操作数组
```
// js代码

function flattenByDeep(array, deep = 1) { // 默认一层
  return array.reduce(
    (target, current) =>
      Array.isArray(current) && deep > 1 ?
        target.concat(flattenByDeep(current, deep - 1)) : // 下一次减一层
        target.concat(current)
    , [])
}
```
# 最值
## 利用 `reduce`
`reduce` 函数真的是超级好用，

```
// js代码

array.reduce((c,n) => Math.max(c,n))
```

## Math.max
`Math.max` 参数原本是一组数字，只需要让他可以接收数组即可。
```
// js代码

const array = [3,2,1,4,5];
Math.max.apply(null,array);
Math.max(...array);

```