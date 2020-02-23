---
title: Vue 响应式原理的实现（课程笔记）
date: 2020-02-21 23:00:00
categories: 
	- 源码原理
tags:
	- vue
---
> 作者：李旭光
> 引用请标明出处

# 前言
最近一直在追网课，说实话从业9年了，一直觉得前端发展非常快，而且一直充满着危机感，每天都要学习进步才有安稳的感觉，今天听了 vue 响应式原理实现的公开课，感觉还不错，做了如下笔记，帮助自己记忆，也希望能帮助大家。

# Vue2 原理
## 什么是 defineProperty
defineProperty 其实是定义对象属性用的
> defineProperty 其实并不是核心的为一个对象做数据双向绑定，而是去给对象做属性标签，只不过是属性里 get 和 set 实现了响应式。

| 属性名       | 默认值    |
| ------------ | --------- |
| value        | undefined |
| get          | undefined |
| set          | undefined |
| writable     | false     |
| enumerable   | false     |
| configurable | false     |

``` js
var ob = {
	a:1,
	b:2
}
// 参数 1、对象 2、属性 3、配置
Object.defineProperty(ob,'a',{
	writable:false,
	enumerable:true,
	configurable:true,
})
console.log(Object.getOwnPropertyDescriptor(ob,'a')) // 1
ob.a = 2
console.log(ob.a) // 1
```
下面我们实现一下双向绑定

``` js
Object.defineProperty(ob,'a',{
	get:function(){
		console.log('a is be get')
		return 999;
	}, 
	set:function(){
		console.log('a is be set')
		return 999;
	}, 
})

console.log(Object.getOwnPropertyDescriptor(ob,'a')) // 999

```
改造代码实现双向绑定（存取值）
``` js
var _val = obj.a; // 暂存
Object.defineProperty(ob,'a',{
	get:function(){
		console.log('a is be get')
		return _val;
	}, 
	set:function(newVal){
		_val = newVal // 新值替换旧值
		console.log('a is be set')
		return _val;
	}, 
})
```
## Vue 中从改变一个数据到发生改变的过程
1. 改变数据触发 Set 
2. Set 部分触发 notify（更新）
   1. Get 部分收集依赖
3. 更改对应的虚拟 Dom
4. 重新 Render

``` js
// MyVue.js

// 简单版本 vue
function MyVue(){
	this.$data = {
		a: {
			b:1
		}, 
		c:2
	}
	this.el = document.getElementById('app');
	this.virtualDom = '';
	this.observer(this.$data);
	this.render();
}
vue.property.observer = function(obj){
	var _val, self = this;
	// var dep = new Dep() -> 源码中依赖收集对象
	for(var key in obj){ // 属性有可能是对象，要递归绑定
		_val = obj[key];
		if(typeof _val === 'Object'){
			this.observer(_val)
		}else{
			Object.defineProperty(this.$data,key,{ // 这里是实际绑定过程
				get:function(){
					// 依赖收集
					// dep.depend(); -> vue 源码中收集依赖的方法
					return _val
				},
				set:function(newVal){
					_val = newVal
					// dep.notify(); -> vue 源码中
					self.render() // AST语法树
				}
			})
		}
	}
}
vue.property.render = function(){
	this.virtualDom = 'i am '+this.$data.b;
	this.el.innerHTML = this.virtualDom;
}
```
``` html
// index.html

<!DOCTYPE html>
<html>
	<head>
		<title>自己实现Vue2数据双向绑定</title>
	</head>
	<body>
		<div id="app"></div>
		<script type="text/javascript" src='myVue.js'></script>
		<script type="text/javascript">
			var mv = new MyVue();
			setTimeout(function(){
				console.log('changes');
				console.log(mv.$data);
				mv.$data.b = 222;
			})
		</script>
	</body>
</html>
```
> 依赖收集：
> 1. 我们的data里面的数据并不是所有地方都用到
> 2. 如果我们直接更新整个视图，浪费资源
> 3. 先收集依赖改变的数据的组件，再更新依赖了数据的组件（Dep depend notify）

### 格外注意的地方---数组怎么监听
definePropty 只能给对象进行 get set 绑定， 数组怎么办？

vue 中 使用了 ***装饰者模式***
> 装饰者模式 Decorator模式（别名Wrapper）：动态将职责附加到对象上，若要扩展功能，装饰者提供了比继承更具弹性的代替方案。

``` js
var arraypro = Array.property; // 创建一个数组的原型对象
var arrob = Object.create(arraypro); // 避免影响原型链
var arr = ['push','pop','shift'];
arr.forEach(function(method,index){
	arrob[method]=function(){ // 装饰者模式
		var ret = arraypro[method].apply(this,arguments)
		dep.notify() // 扩展了功能
	}
})

``` 
## Vue3 实现双向绑定
Proxy 是什么？
> Proxy 对象用于定义基本操作的自定义行为
> 和 definePropty 类似，功能几乎一样，只是用法上不同
> 1. 不会污染原对象
> 2. 直接给对象就可以了
> 3. 不需要借助外部变量 _val
``` js
var ob = {
	a:1,
	b:2
}

var newOb = new Proxy(ob,{
	get(target,key,receiver){ // target 对象，key 属性
		console.log(target,key,receiver)
		return target[key]
	},
	set(target,key,value,receiver){
		return Reflect.set(target.key,value); 
		// 将值分配给属性的函数。返回一个Boolean，如果更新成功，则返回true。
		// return target[key] = value
	}
})
```
### 为什么改用 Proxy
1. defineProperty 只能监听某个属性，不能全对象监听
2. 可以省去`for in`循环提升代码执行效率
3. 可以监听数组，不需要再为数组做特异性操作
4. 不污染原对象
5. 更优雅

我们用 Proxy 实现一下 observe 方法
``` js
vue.property.observe = function(){
	var self = this;
	this.$data = new Proxy(this.$data,{
		get(target,key, receiver){
			return target[key]
		},
		set(target,key,newVal){
			target[key] = newVal
			self.render()
		}
	})
}
```

#### 还能用 Proxy 做什么
1. 校验类型
2. 真正的私有变量

##### 校验类型
例子：
``` js
// 数据类型验证
// 我们要创建一个对象，这个对象是个人，他有name和age两个属性
// name必须是中文，age必须是数字，大于18岁

// 这里用到了策略模式
var valid = {
	name(value){
		var reg=/^[\u4E00-\u9FAS]=$/
		if(typeof value === 'string' && reg.test(value)){
			return true;
		}
		return false;
	}, 
	age(value){
		if(typeof value === 'number' && value > 18){
			return true;
		}
		return false;
	}
}
function Person(name,age){
	this.name = name
	this.age = age
	return new Proxy(this,{
		get(target,key){
			return target[key]
		}, 
		set(target,key,value){
			if(valid[key](value)){
				return Reflect.set(target,key,value)
			}else{
				throw new Error(key+'is not valid')
			}
		}
	})
}
new Person('name',19)
```
> ***策略模式***
> 定义一系列的算法,把它们一个个封装起来, 并且使它们可相互替换。

##### 真正的私有变量
vue-router 源码中，给 $router ,$route 用 defineProperty 定义 get 并返回本身，这样就不能修改属性了。
``` js
Object.defineProperty(this,'$router',{ // Router 的实例
	get(){
		return this._root._router;
	}
})
Object.defineProperty(this,'$route',{
	get(){
		return {
			// 当前路由所在的状态
			current: this._root._router.history.current;
		}
	}
})
```

## 虚拟Dom和diff算法
虚拟Dom是虚拟的，他只在概念里面存在，在AST语法树，下面进行解释
``` js
// 模板
<template>
	<div>
		<p>{{msg}}</p>
		<p>2</p>
		<p>3</p>
	</div>
</template>

// diff 描述法
diff <div>
	props:{
		id:2
	}
	children:[
		diff <p>
			props:{
				id:xxx
			}
			children:[
				...
			]
	]

// 对象描述法
var virtual = {
	dom:'div',
	props:{
		id:2
	},
	children:[
		....
	]
}
```
每层结构都是一样的，那么是如何进行 diff 比对的呢？
``` js
/** 
 * diff 算法
 */
patchVnode(oldVnode,vnode){ // 接收新旧节点
	const el = vnode.el = oldVnode.el; // 拿出真实dom
	let i,oldCh = oldVnode.children ,ch = vnode.children // 拿出新旧节点子元素children数组
	if(oldVnode === vnode) return; // 新旧节点一致，直接返回不进行后续操作
	// 分情况操作 --- 只有文字节点，删除了子元素，增加了子元素，子元素发生变动
	// 新旧节点都不为空，且不一样
	if(oldVnode.text !== null && vnode.text !== null && oldVnode.text !== vnode.text){ // 文字发生更新
		api.setTextContent(el,vonde.text) // 文字发生了更新，更新文字，给Dom，更新文字
	} else { // 不是单纯文字节点的话
		updateEle(); // 更新元素
		if(oldCh&&ch&&oldCh!==ch){ // 都有子元素，子元素变动
			updateChildren() // 调用更新子元素方法
		} else if(ch){ // 增加子元素
			createEl(vnode) // 创建子元素
		} else if(oldCh){ // 删除子元素
			api.removeChildren(el) // 调用删除子元素方法
		}
	}
}
```
源码要多看，以下必看 ***Vue/react/axios/vue-router/Redux/Vuex***
为什么要看源码？？
- 初级前端就会用vue或react --- 从差不多水平的60%中挑出更好的人
- 提高思想--》看优秀的代码--》写优秀的代码
- 看源码能力，对高级前端是必备的。--- 解决疑难杂症，看源码了解原理。

## vue 性能优化
因为是公开课，所以时间上没来的及说完，以后自己在听别的有关的内容时再补上这块。

# 最后
只有不断学习才能进步，充分利用网络的便利性，找各种优质的教学资源，我相信，努力会有回报，加油！