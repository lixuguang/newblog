---
title: es6中箭头函数没了arguments怎么办？
date: 2020-01-22 14:50:21
categories: 
	- 前端技术
tags: 
	- es6
---
> 作者：李旭光
> 引用请标明出处


# 前言
es6中，arguments被取消掉了，但是函数还是会有参数不确定的时候，那么我们该怎么办呢，接下来将介绍一下如何在es6中完成arguments一样的效果。
<!-- more -->

## 剩余运算符
``` js
// js代码

let func = (...rest) => {
  console.log(rest)
  //[1,2,3]
}

func(1,2,3)
```

看上面的代码，有的朋友会问，这`...`的操作不应该是展开运算符么？是的，扩展运算符与剩余操作符都是以三点开头的操作符，二者长的很像，只是在用法上有些差别。它们已经被 ES6 数组支持，能解决很多之前 arguments 解决起来很麻烦的问题。

简单来说剩余运算是在参数上使用的。
