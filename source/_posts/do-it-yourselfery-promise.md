---
title: 自己动手实现系列 ---- promise
date: 2020-01-13 08:40:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- promise
---
# 前言
面试中时常会出现需要手动实现某个功能的要求，所以我准备整理一个自己动手实现系列文章，这一次呢就是自己动手实现 `promise`

## 实现 `promise` 思路

**基础步骤**

1. 设定三个状态 `PENDING` 、 `FULFILLED` 、 `REJECTED`  ，只能由 `PENDING` 改变为 `FULFILLED` 、 `REJECTED` ，并且只能改变一次
2. `MyPromise` 接收一个函数 `executor` ， `executor` 有两个参数 `resolve` 方法和 `reject` 方法
3. `resolve` 将 `PENDING` 改变为 `FULFILLED`
4. `reject` 将 `PENDING` 改变为 `FULFILLED`
5. `promise` 变为 `FULFILLED` 状态后具有一个唯一的 `value`
6. `promise` 变为 `REJECTED` 状态后具有一个唯一的 `reason`

** `then` 方法**
1. `then` 方法接受两个参数 `onFulfilled` 、 `onRejected` ，它们分别在状态由 `PENDING` 改变为 `FULFILLED` 、 `REJECTED` 后调用
2. 一个 `promise` 可绑定多个 `then` 方法
3. `then` 方法可以同步调用也可以异步调用
4. 同步调用：状态已经改变，直接调用 `onFulfilled` 方法
5. 异步调用：状态还是 `PENDING` ，将 `onFulfilled` 、 `onRejected` 分别加入两个函数数组 `onFulfilledCallbacks` 、 `onRejectedCallbacks` ，当异步调用 `resolve` 和 `reject` 时，将两个数组中绑定的事件循环执行。


## 代码实现
``` js
// js代码

// 定义状态常量
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

/**
 * 定义MyPromise模拟Promise
 * @param {func} executor 接收函数
*/
function MyPromise(executor) {
	this.state = PENDING; // 默认状态为 pending
	this.value = null;
	this.reason = null;

	// 定义成功失败的函数数组
	this.onFulfilledCallbacks = [];
  this.onRejectedCallbacks = [];

	// 定义成功回调
	const resolve = (value) => {
		if (this.state === PENDING) {
			this.state = FULFILLED;
			this.value = value;

			this.onFulfilledCallbacks.forEach(func => {
				func();
			});
		}
	}

	// 定义失败回调
	const reject = (reason) => {
		if (this.state === PENDING) {
			this.state = REJECTED;
			this.reason = reason;
			this.onRejectedCallbacks.forEach(func => {
				func();
			});
		}
	}

	try {
		executor(resolve, reject);
	} catch (reason) {
		reject(reason);
	}
}

MyPromise.prototype.then = function (onFulfilled, onRejected) {
	switch (this.state) {
		case FULFILLED:
			onFulfilled(this.value);
			break;
		case REJECTED:
			onFulfilled(this.value);
			break;
		case PENDING:
			this.onFulfilledCallbacks.push(() => {
				onFulfilled(this.value);
			})
			this.onRejectedCallbacks.push(() => {
				onRejected(this.reason);
			})
			break;
}
```
## then方法异步调用
如下面的代码：输入顺序是：1、2、ConardLi

``` js
// js代码

console.log(1);

let promise = new Promise((resolve, reject) => {
  resolve('ConardLi');
});

promise.then((value) => {
  console.log(value);
});

console.log(2);
```
虽然 `resolve` 是同步执行的，我们必须保证 `then` 是异步调用的，我们用 `setTimeout` 来模拟异步调用（并不能实现微任务和宏任务的执行机制，只是保证异步调用）
``` js

// js代码
MyPromise.prototype.then = function (onFulfilled, onRejected) {
	if (typeof onFulfilled != 'function') {
		onFulfilled = function (value) {
			return value;
		}
	}
	if (typeof onRejected != 'function') {
		onRejected = function (reason) {
			throw reason;
		}
	}
	switch (this.state) {
		case FULFILLED:
			setTimeout(() => {
				onFulfilled(this.value);
			}, 0);
			break;
		case REJECTED:
			setTimeout(() => {
				onRejected(this.reason);
			}, 0);
			break;
		case PENDING:
			this.onFulfilledCallbacks.push(() => {
				setTimeout(() => {
					onFulfilled(this.value);
				}, 0);
			})
			this.onRejectedCallbacks.push(() => {
				setTimeout(() => {
					onRejected(this.reason);
				}, 0);
			})
			break;
	}
}
```
## then方法链式调用
保证链式调用，即 `then` 方法中要返回一个新的 `promise` ，并将 `then` 方法的返回值进行 `resolve` 。

*注意：这种实现并不能保证 `then` 方法中返回一个新的 `promise` ，只能保证链式调用*。

``` js
// js代码
MyPromise.prototype.then = function (onFulfilled, onRejected) {
	if (typeof onFulfilled != 'function') {
		onFulfilled = function (value) {
			return value;
		}
	}
	if (typeof onRejected != 'function') {
		onRejected = function (reason) {
			throw reason;
		}
	}

	// 创建一个新的MyPromise对象
	const promise2 = new MyPromise((resolve, reject) => {
		switch (this.state) {
			case FULFILLED:
				setTimeout(() => {
					try {
						const x = onFulfilled(this.value);
						resolve(x);
					} catch (reason) {
						reject(reason);
					}
				}, 0);
				break;
			case REJECTED:
				setTimeout(() => {
					try {
						const x = onRejected(this.reason);
						resolve(x);
					} catch (reason) {
						reject(reason);
					}
				}, 0);
				break;
			case PENDING:
				this.onFulfilledCallbacks.push(() => {
					setTimeout(() => {
						try {
							const x = onFulfilled(this.value);
							resolve(x);
						} catch (reason) {
							reject(reason);
						}
					}, 0);
				})
				this.onRejectedCallbacks.push(() => {
					setTimeout(() => {
						try {
							const x = onRejected(this.reason);
							resolve(x);
						} catch (reason) {
							reject(reason);
						}
					}, 0);
				})
				break;
		}
	})
	return promise2;
}
```
## catch方法
若上面没有定义 `reject` 方法，所有的异常会走向 `catch` 方法：
``` js
// js代码

MyPromise.prototype.catch = function(onRejected) {
  return this.then(null, onRejected);
};

```
## finally方法
不管是 `resolve` 还是 `reject` 都会调用 `finally` 。
``` js
// js代码

MyPromise.prototype.finally = function(fn) {
	return this.then(value => {
		fn();
		return value;
	}, reason => {
		fn();
		throw reason;
	});
};
```
## Promise.resolve
`Promise.resolve` 用来生成一个直接处于 `FULFILLED` 状态的 `Promise` 。
``` js
// js代码
MyPromise.reject = function(value) {
  return new MyPromise((resolve, reject) => {
    resolve(value);
  });
};
```
## Promise.reject
`Promise.reject` 用来生成一个直接处于 `REJECTED` 状态的 `Promise` 。
``` js
// js代码
MyPromise.reject = function(reason) {
  return new MyPromise((resolve, reject) => {
    reject(reason);
  });
};
```
## all方法
接受一个 `promise` 数组，当所有 `promise` 状态 `resolve` 后，执行 `resolve`
``` js
// js代码
MyPromise.all = function (promises) {
	return new Promise((resolve, reject) => {
		if (promises.length === 0) {
			resolve([]);
		} else {
			let result = [];
			let index = 0;
			for (let i = 0; i < promises.length; i++) {
				promises[i].then(data => {
					result[i] = data;
					if (++index === promises.length) {
						resolve(result);
					}
				}, err => {
					reject(err);
					return;
				});
			}
		}
	});
}
```
## race方法
接受一个 `promise` 数组，当有一个 `promise` 状态 `resolve` 后，执行 `resolve`

``` js
// js代码
MyPromise.race = function (promises) {
	return new Promise((resolve, reject) => {
		if (promises.length === 0) {
			resolve();
		} else {
			let index = 0;
			for (let i = 0; i < promises.length; i++) {
				promises[i].then(data => {
					resolve(data);
				}, err => {
					reject(err);
					return;
				});
			}
		}
	});
}
```

# 最后
如此一个自定义的 `promise` 就实现了，怎么样学回来吗？