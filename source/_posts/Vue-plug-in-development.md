---
title: vue插件开发（笔记）
date: 2020-02-24 22:00:00
categories: 
	- 前端框架
	- VUE
tags:
	- 插件
---
> 作者：李旭光
> 引用请标明出处

# vue插件开发
> Vue.use({install(Vues){}})
## Vue.use
把给到的内容执行一下
举例
``` js
function a(){
	console.log('a')
}
Vue.use(a) // a
```
有 install 就执行 install
``` js
function a(){
	console.log('a')
}
a.install = function(){
	console.log('b')
}
Vue.use(a) // b
```
再进一步
``` js
function a(){
	console.log('a')
}
a.install = function(){
	// console.log('b')
	vue.mixin({ // 抽离公共逻辑 ， 缺点：命名冲突，难以阅读
		data(){ // data数据少的时候可以不用vuex 用mixin
			return {
				c:'this is mixin'
			}
		},
		methods:{
			// 混入方法
			// 提示性弹窗 原：import 控制 显隐 现在：在根节点引入，通过mixin在方法控制显隐
		}
		// 混入生命周期
		create(){
			// 所有组件的create生命周期都执行 mixin先执行
		}
	})
}
Vue.use(a) // b
```

vue.util.defineReactive()
``` js
vue.util.defineReactive()

var test = {
	testa: 1
}
setTimeout(()=>{
	test.testa = 2
},1000)
vue.mixin({
	beforeCreate(){
		this.test = test
	}
})

```

vue.extend vue.util.extend

vue.util.extend ===> 简单做了个拷贝,拷贝到一起
``` js
vue.util.extend(a,b)
```
vue.extend ===> 获取到某个对象的实例
``` js
let Constrator = vue.extend(obj)
let vm = new Constrator()
```

手写vue-router
``` js
// myVueRouter.js
class HistoryRoute(){
	constructor(){
		this.current = null;
	}
}

class vueRouter{
	constructor(options){
		this.mode = options.mode || 'hash'
		this.history = new HistoryRoute
		this.routes = options.routes||[]
		this.routesMap = this.createMap(this.routes)
		this.init()
	}
	init(){
		if(this.mode == 'hash'){
			// 自动加上 #
			location.hash?"":location.hash="/"
			window.addEventListener('load',()=>{
				this.history.current = location.hash.slice(1)
			})
			window.addEventListener('hashchange',()=>{
				this.history.current = location.hash.slice(1)
			})
		}else{
			location.pathname?"":location.pathname="/"
			window.addEventListener('load',()=>{
				this.history.current = location.hash.pathname
			})
			window.addEventListener('popstate',()=>{
				this.history.current = location.hash.pathname
			})
		}
	}
	createMap(router){
		return router.reduce((memo,current)=>{
			memo[current.path] = current.component
		})			
	}
}

vueRouter.install = function(Vue){
	Vue.mixin({
		beforeCreate(){ // 组件还未实例化好
			if(this.$options && this.$options.router){ // 有配置而且引入路由
				this._root = this
				this._router = this.$option.router

				Vue.util.defineReactive(this,'current',this._router.history)
			}else{
				this._root = this.$parent._root
			}
			// 增强健壮性
			Object.defineProperty(this,'$route',{
				get(){
					return this._root._router
				}
			})
		}
	})
	Vue.component('router-view',{
		render(h){
			// 如何根据当前的current，获取到对应的组件
			let current = this._self._root._router.history.current
			let routerMap = this._self._root._router.routeMap
			return h(routeMap[current])
		}
	})
}
```