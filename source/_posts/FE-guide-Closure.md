---
title: 前端常见知识点整理 ---- 闭包
date: 2020-01-02 21:32:22
categories: 
  - 前端技术
  - 前端常见知识点整理
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处


# 闭包`Closure` 
闭包的定义很简单：函数 `A` 返回了一个函数 `B` ，并且函数 `B` 中使用了函数 `A` 的变量，函数 `B` 就被称为闭包。

```
// js代码

function A() {
  let a = 1
  function B() {
      console.log(a)
  }
  return B
}
```
你是否会疑惑，为什么函数 `A` 已经弹出调用栈了，为什么函数 `B` 还能引用到函数 `A` 中的变量。因为函数 `A` 中的变量这时候是存储在堆上的。现在的 `JS` 引擎可以通过***逃逸分析***辨别出哪些变量需要存储在堆上，哪些需要存储在栈上。

经典面试题，循环中使用闭包解决 `var` 定义函数的问题

```
// js代码

for ( var i=1; i<=5; i++) {
	setTimeout( function timer() {
		console.log( i );
	}, i*1000 );
}
```
首先因为 `setTimeout` 是个异步函数，所有会先把循环全部执行完毕，这时候 `i` 就是 6 了，所以会输出一堆 6。

解决办法两种，第一种使用闭包

```
// js代码

for (var i = 1; i <= 5; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000);
  })(i);
}
```
第二种就是使用 `setTimeout` 的第三个参数(附加参数，传给 `setTimeout` 中的函数)

```
// js代码

for ( var i=1; i<=5; i++) {
	setTimeout( function timer(j) {
		console.log( j );
	}, i*1000, i);
}
```

第三种就是使用 `let` 定义 `i` 了
```
// js代码

for ( let i=1; i<=5; i++) {
	setTimeout( function timer() {
		console.log( i );
	}, i*1000 );
}
```
因为对于 `let` 来说，他会创建一个块级作用域，相当于

```
// js代码

{ // 形成块级作用域
  let i = 0
  {
    let ii = i
    setTimeout( function timer() {
        console.log( ii );
    }, i*1000 );
  }
  i++
  {
    let ii = i
  }
  i++
  {
    let ii = i
  }
  ...
}
```