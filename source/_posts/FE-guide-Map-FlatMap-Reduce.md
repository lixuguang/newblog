---
title: 前端常见知识点整理 ---- Map、FlatMap 和 Reduce
date: 2020-01-03 11:35:00
categories: 
  - 前端技术
  - 前端常见知识点整理
tags: 
	- 面试
---
> 作者：李旭光
> 引用请标明出处


# Map、FlatMap 和 Reduce
## `Map`
`Map` 作用是生成一个新数组，遍历原数组，将每个元素拿出来做一些变换然后 `append` 到新的数组中。

```
// js代码

[1, 2, 3].map((v) => v + 1) // -> [2, 3, 4]
```
`Map` 有三个参数，分别是***当前索引元素***，***索引***，***原数组***
## `FlatMap`
`FlatMap` 和 `map` 的作用几乎是相同的，但是对于多维数组来说，会将原数组降维。可以将 `FlatMap` 看成是 `map + flatten` ，目前该函数在浏览器中还不支持。


```
// js代码

[1, [2], 3].flatMap((v) => v + 1) // -> [2, 3, 4]
```
如果想将一个多维数组彻底的降维，可以这样实现

```
const flattenDeep = (arr) => Array.isArray(arr)
  ? arr.reduce( (a, b) => [...a, ...flattenDeep(b)] , [])
  : [arr]

flattenDeep([1, [[2], [3, [4]], 5]])
```

## Reduce 升序执行
`Reduce` 作用是数组中的值组合起来，最终得到一个值
`reduce()` 方法对数组中的每个元素执行一个由您提供的 `reducer` 函数(升序执行)，将其结果汇总为单个返回值。

`reducer` 函数接收4个参数:

- Accumulator (acc) (累计器)
- Current Value (cur) (当前值)
- Current Index (idx) (当前索引)
- Source Array (src) (源数组)
您的 `reducer` 函数的返回值分配给累计器，该返回值在数组的每个迭代中被记住，并最后成为最终的单个结果值。

```
// js代码

function a() {
    console.log(1);
}

function b() {
    console.log(2);
}

[a, b].reduce((a, b) => a(b()))
// -> 2 1
```