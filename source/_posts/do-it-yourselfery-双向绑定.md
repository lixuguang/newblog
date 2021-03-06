---
title: 自己动手实现系列 ---- Vue双向绑定
date: 2020-03-24 23:40:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- 自己动手实现系列
---
# Vue 2.x 的 `Object.defineProperty` 版本

``` js
// 数据
const data = {
  text: 'default'
};
const input = document.getElementById('input');
const span = document.getElementById('span');

// 数据劫持
Object.defineProperty(data, 'text', {
  // 数据变化 —> 修改视图
  set(newVal) {
    input.value = newVal;
    span.innerHTML = newVal;
  }
});

// 视图更改 --> 数据变化
input.addEventListener('keyup', function(e) {
  data.text = e.target.value;
});
```

# Vue 3.x 的 `proxy` 版本
``` js
// 数据
const data = {
  text: 'default'
};
const input = document.getElementById('input');
const span = document.getElementById('span');

// 数据劫持
const handler = {
  set(target, key, value) {
    target[key] = value;
    // 数据变化 —> 修改视图
    input.value = value;
    span.innerHTML = value;
    return value;
  }
};
const proxy = new Proxy(data, handler);

// 视图更改 --> 数据变化
input.addEventListener('keyup', function(e) {
  proxy.text = e.target.value;
});
```