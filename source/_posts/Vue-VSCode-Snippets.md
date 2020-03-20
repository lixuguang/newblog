---
title: Vue VSCode Snippets 自动生成Vue代码片段的VSCode扩展
date: 2017-08-30 16:23:17
categories: 
	- 效率工具
	- VSCode插件
tags: 
	- VUE
	- 插件
---
> 作者：李旭光
> 引用请标明出处


# 前言
现代化的IDE已经把开发者变得越来越懒，但是我觉得这样挺好的，让工具完成手工反复重复的工作，提高工作效率的同时降低开发者的疲劳感，Vue VSCode Snippets就是这样一个VSC插件，它可以用简单的几个字母就敲出一整块代码片段，在学习和做VUE项目时可以极大地提高工作效率，下面就介绍一下插件的常见命令。
<!-- more -->

此插件可用比较简单的写法生成代码片段，非常适合开发工作，减少代码工作量。

Script


Snippet | Purpose
---|---
vbase | Single file component base
vbase|	Single file component base
vdata|	Component data as a function
vmethod|	Vue method
vcomputed|	Vue computed property
vwatcher|	Vue watcher with new and old value args
vprops|	Props with type and default
vimport	|Import one component into another
vimport-c|	Import one component into another within the export statement
vimport-export|	Import one component into another and use it within the export statement
vfilter	|Vue filter
vmixin|	Create a Vue Mixin
vmixin-use|	Bring a mixin into a component to use
vc-direct|	Vue create a custom directive
vimport-lib	|Import a library
vimport-gsap|	Import GreenSock with Timeline and Eases
vanimhook-js|	Using the Transition component JS hooks in methods
<!-- more -->
Template

Snippet | Purpose
---|---
vfor|	v-for directive
vmodel|	Semantic v-model directive
vmodel-num|	Semantic v-model number directive
von	|v-on click handler with arguments
vel-props|	Component element with props
vsrc|	Image src binding
vstyle|	Inline style binding
vstyle-obj|	Inline style binding with objects
vclass|	Class binding
vclass-obj|	Class binding with objects
vclass-obj-mult	|Multiple conditional class bindings
vanim|	Transition component with JS hooks

Vuex

Snippet | Purpose
---|---
vstore|	Base for Vuex store.js
vgetters|	Vuex Getter
vmutation|	Vuex Mutation
vaction|	Vuex Action
vstore-import|	Import vuex store into main.js

Extra (plaintext)

Snippet | Purpose
---|---
gitignore|	.gitignore file presets
vinc|	incrementer
vdec|	decrementer