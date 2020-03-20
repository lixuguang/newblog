---
title: SSR
date: 2020-02-11 16:00:00
categories: 
	- 前端技术
tags:
	- 同构
	- 服务端渲染
---
> 作者：李旭光
> 引用请标明出处

# 什么是SSR
## 传统浏览器的vue纯浏览器渲染
浏览器请求服务器，服务器返回静态资源 ，客户端负责渲染js
## ssr
浏览器请求服务器，node服务端渲染js后，返回对应的页面返回给浏览器
## SSR需要那些东西
## 手写SSR
特性：
- 每一次访问必须新建一个vue实例
- 只会触发组件的 beforeCreate和created钩子

核心库
- vue
- vue-server-renderer
# vue + next