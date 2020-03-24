---
title: 自己动手实现系列 ---- EventEmitter(事件触发器)
date: 2020-03-24 23:45:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- 自己动手实现系列
---
# 实现一个 `EventEmitter`
1. 创建一个 `Event` 类，包含***构造函数、绑定、解绑、触发***方法
2. `on` 监听event事件，事件触发时调用fn函数。根据字典创建事件数组，如果事件处理不存在，那么就推入数组，反之返回原数组
3. `off` 从字典中获取当前事件数组，如果获取值是数组，那么判断解绑哪个事件，如果没填清空全部（数组长度置为零），否则从事件数组删除选择的事件
4. `emit` 触发event事件，并把参数arg1,arg2,arg3....传给事件处理函数，跟解绑外层判断一样，内层把删除改为执行即可
5. `once` 为指定事件注册一个单次监听器，单次监听器最多只触发一次，触发后立即解除监听器。

``` js
class Event {
  constructor () {
    // 储存事件的数据结构
    // 为查找迅速， 使用对象（字典）
    this._cache = {}
  }

  // 绑定
  on(event, callback) {
    // 为了按类查找方便和节省空间
    // 将同一类型事件放到一个数组中
    // 这里的数组是队列， 遵循先进先出
    // 即新绑定的事件先触发
    let fns = (this._cache[event] = this._cache[event] || [])
    if(fns.indexOf(callback) === -1) {
      fns.push(callback)
    }
    return this
    }

  // 解绑
  off (event, callback) {
    let fns = this._cache[event]
    if(Array.isArray(fns)) {
      if(callback) {
        let index = fns.indexOf(callback)
        if(index !== -1) {
          fns.splice(index, 1)
        }
      } else {
        // 全部清空
        fns.length = 0
      }
    }
    return this
  }
  // 触发emit
  emit(event, ...args) {
    let fns = this._cache[event]
    if(Array.isArray(fns)) {
      fns.forEach((fn) => {
        fn(...args)
      })
    }
    return this
  }

  // 一次性绑定
  once(event, callback) {
    let onceCallback = () => { // 定义一个只执行一次就解绑的方法
      callback.call(this); // 使用call改变this指向
      this.off(event, onceCallback); // 解绑
    };
    this.on(event, onceCallback); // 绑定
    return this;
  }
}
```
好的接下来我们调用一下
``` js

let e = new Event()

e.on('click',function(){
  console.log('on')
})
// e.trigger('click', '666')
console.log(e)
```