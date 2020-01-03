---
title: 前端常见知识点整理 ---- Proxy
date: 2020-01-03 12:00:00
categories: 
	- 前端技术
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处


# Proxy
`Proxy` 是 `ES6` 中新增的功能，可以用来自定义对象中的操作

```
// js代码

let p = new Proxy(target, handler);
// `target` 代表需要添加代理的对象
// `handler` 用来自定义对象中的操作
```
可以很方便的使用 `Proxy` 来实现一个数据绑定和监听

```
let onWatch = (obj, setBind, getLogger) => {
  let handler = {
    get(target, property, receiver) {
      getLogger(target, property)
      return Reflect.get(target, property, receiver);
    },
    set(target, property, value, receiver) {
      setBind(value);
      return Reflect.set(target, property, value);
    }
  };
  return new Proxy(obj, handler);
};

let obj = { a: 1 }
let value
let p = onWatch(obj, (v) => {
  value = v
}, (target, property) => {
  console.log(`Get '${property}' = ${target[property]}`);
})
p.a = 2 // bind `value` to `2`
p.a // -> Get 'a' = 2
```