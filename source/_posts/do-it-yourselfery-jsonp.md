---
title: 自己动手实现系列 ---- jsonp
date: 2020-01-09 16:40:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- jsonp
---
# 前言
面试中时常会出现需要手动实现某个功能的要求，所以我准备整理一个自己动手实现系列文章，第一个呢就是自己动手实现 `jsonp`

## 实现 `jsonp` 思路
1. 将传入的data数据转化为url字符串形式
2. 处理url中的回调函数
3. 创建一个script标签并插入到页面中
4. 挂载回调函数

## 代码实现
```
// js代码

(function (window,document) {
	"use strict";
	var jsonp = function (url,data,callback) {
		// 1.将传入的data数据转化为url字符串形式
		// {id:1,name:'jack'} => id=1&name=jack
		var dataString = url.indexof('?') == -1? '?': '&';
		for(var key in data){
			dataString += key + '=' + data[key] + '&';
		};

		// 2 处理url中的回调函数
		// cbFuncName回调函数的名字 ：my_json_cb_名字的前缀 + 随机数（把小数点去掉）
		var cbFuncName = 'my_json_cb_' + Math.random().toString().replace('.','');
		dataString += 'callback=' + cbFuncName;

		// 3.创建一个script标签并插入到页面中
		var scriptEle = document.createElement('script');
		scriptEle.src = url + dataString;

		// 4.挂载回调函数
		window[cbFuncName] = function (data) {
			callback(data); // 处理完回调函数的数据之后，删除jsonp的script标签
			document.body.removeChild(scriptEle);
		}
		document.body.appendChild(scriptEle);
	}
	window.$jsonp = jsonp;
})(window,document)
```