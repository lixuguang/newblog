---
title: 微信的H5兼容方案
date: 2019-12-31 1:37:08
categories: 
	- 前端开发
tags: 
	- 微信
	- 兼容
---
> 作者：李旭光
> 引用请标明出处


# 前言

最近被公司新来的前端问到了一些微信页面的兼容性问题，因为我做微信开发的经验比较少，被问到了真是感到很尴尬，所以在搜到了解决方案告诉了他之后，我又深入的再网上找了一些微信开发中遇到的兼容问题，特此整理，以留后用。
<!-- more -->

## 1、ios端兼容input光标的高度
***bug描述：***
这个问题只出现在苹果手机上，在安卓手机上显示没有问题，可以说是非常诡异，简单描述一下就是在input输入框聚焦时，光标大小应该跟字号一直，但是在苹果手机上当点击输入的时候，光标的高度和父盒子的高度一样。
***分析：***
说来主要是习惯导致的问题，通常我们习惯将height和line-height设置成一样的值，这个时候input光标就会整个变得很大。
***解决：***
实际上解决方案也很简单，就是不设置行高，通过padding来控制输入内容与外框的距离。
```
// less代码
.input-x{
	height:40px;
	// line-height:40px; // 此行注释掉
	.input-inline{
		padding: 10px 0;
	}
}
```
这样做问题就解决了。

## 2、ios端微信h5页面上下滑动会卡顿，页面会有缺失
***bug描述：***
没错又是ios端，当页面高度超过一屏，那么上下滑动时就会出现页面卡顿的情况，而且时有伴随内容不能全部显示的情况。
***分析：***
这里实际上是浏览器内核解析不同导致的问题，在Andriod设备上，微信调用的是Webkit内核，而ios中是使用了Safari的内核，Safari对于滚动事件（overflow-scrolling）会使用原生的控件。而webkit内核则会创建一个UIScrollView来提供给子layer用以渲染。
***解决：***
在做样式重置时，加上下面这句话就能解决这个问题。
```
// css代码
*{
	-webkit-overflow-scrolling: touch;
}
```
但是这个方案也有缺陷，就是页面中不能有使用absolute定位的元素，不然布局就错乱了。
***延伸：***
-webkit-overflow-scrolling 属性控制元素在移动设备上是否使用滚动回弹效果.
- auto: 使用普通滚动, 当手指从触摸屏上移开，滚动会立即停止。
- touch: 使用具有回弹效果的滚动, 当手指从触摸屏上移开，内容会继续保持一段时间的滚动效果。继续滚动的速度和持续的时间和滚动手势的强烈程度成正比。同时也会创建一个新的堆栈上下文。

## 3、ios键盘唤起再收起，页面不会恢复原位
***bug描述：***
哎，对的还是ios，问题标题描述的比较清晰了，就是键盘弹出时，页面内容会整体上移，但是收起键盘时本应回归原位的不回去了。—_—|||
***分析：***
固定定位的元素，如果元素内input框聚焦的时候会弹出软键盘，软键盘会占用屏幕面积，失去焦点时软键盘消失，但是仍会占用，页面就会不能恢复原状，也就导致input框不能再次输入了。
***解决：***
在input失去焦点键盘收起时，写一个监听事件，事例代码如下：
```
// vue代码
<input @blur="changeBlur()"/>

// js代码
changeBlur(){
	let ua = navigator.userAgent; // 获取用户代理
	let app = navigator.appVersion; // 获取客户端版本信息
	let isIos = ua.match(/i[^;]+;( U;)? CPU.+Mac OS X/); // 判断是否是Ios设备
	if(isIos){
		setTimeout(()=>{
			const scrollHeight = document.documentElement.scrollTop || document.body.scrollTop || 0
			window.scrollTop(0,Math.max(scrollHeight - 1), 0)
		},100)
	}
}
```
***延伸：***
在iso的微信开发中，页面元素如果用到了position: fixed进行定位，那么键盘收起时，就会被顶上去，第三方输入法也不例外。

### 4、Android弹出键盘遮挡文本输入框
***bug描述：***
刚才说的问题都是Ios端的，实际上Android上也有挺多坑，上面讲到Ios上输入框弹出键盘的问题后，Android中实际也有，只是现象不同；Andriod中弹出键盘后页面不会向上滑动，但是如果输入框在底部的话会直接被挡住。。。
***分析：***
很坑，因为Andriod中输入框focus后，并不会向上滑动，如果靠下就会被挡住。。
***解决：***
实际上跟Ios上处理差不多的方案，代码如下：
```
// vue代码
<input @blur="changeBlur()"/>

// js代码
changeFocus(){
   let ua = navigator.userAgent;
	 let app = navigator.appVersion;
   let isAndroid = u.indexOf('Android') > -1 || u.indexOf('Linux') > -1;
   if(isAndroid){
      setTimeout(function() {
      document.activeElement.scrollIntoViewIfNeeded();
      document.activeElement.scrollIntoView();
      }, 500);
   }
}
```
***扩展***
Element.scrollIntoView()方法让当前的元素滚动到浏览器窗口的可视区域内。而Element.scrollIntoViewIfNeeded()方法也是用来将不在浏览器窗口的可见区域内的元素滚动到浏览器窗口的可见区域。但如果该元素已经在浏览器窗口的可见区域内，则不会发生滚动

## 5、Vue中路由使用hash模式，分享时Android可分享成功，Ios端分享失败
***bug描述：***
Ios的问题真的挺多的。。。
- 在分享页面给A时，没问题，A把链接分享给B的时候就跳转到首页了；
- 使用Vue-router跳转到第二个页面在分享时候，分享失败；
以上两个问题在Android上均没有问题。
***分析：***
jssdk是后端进行签署，前端校验，但是有时跨域，ios是分享以后会自动带上 from=singlemessage&isappinstalled=0 以及其他参数，分享朋友圈参数还不一样，貌似系统不一样参数也不一样，但是每次获取url并不能获取后面这些参数
***解决：***
- 可以使用改页面this.$router.push跳转，为window.location.href去跳转，而不使用路由跳转，这样可以使地址栏的地址与当前页的地址一样，可以分享成功
- 把入口地址保存在本地，等需要获取签名的时候再取出来，注意：sessionStorage.setItem(‘href’,href); 只在刚进入单应用的时候保存！（还没测试，有点low）

## 写在最后
虽然微信H5方式开发想对来说成本比较低，但是有时候坑开始挺多的，但是微信原生开发又增加了成本，很矛盾，目前能做的就是尽量把踩过的坑都记下来，下次别再跳进去了。