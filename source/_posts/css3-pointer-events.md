---
title: CSS3新属性-pointer-events
date: 2020-05-14
categories: 
  - 前端技术
  - CSS3
tags: 
  - 技巧
---

> 作者：李旭光
> 引用请标明出处

## 前言

今天在看一个叫做Avue的框架组件时看到了一个叫做“全局水印”的组件，功能呢就是给当前页面加上一个水印遮罩，就像下图这样：
![Y08cbd.png](https://s1.ax1x.com/2020/05/14/Y08cbd.png)
因为工作中有这样的场景，所以我很好奇看了一下他的代码，他的水印设置为了fixed定位，并且z-index设置的很高，但是它具有穿透性，最终我发现其中起作用的是一个叫做“pointer-events”的css3新属性，于是为了搞清楚它的作用，我在网上做了一番搜索，并把自己的认识整理一下。

<!-- more -->
## 介绍
MDN上是这样介绍这个属性的 [传送门](https://developer.mozilla.org/zh-CN/docs/Web/CSS/pointer-events)。
> pointer-events CSS 属性指定在什么情况下 (如果有) 某个特定的图形元素可以成为鼠标事件的 target。

它有如下这些个属性值：
``` css
/* Keyword values */
pointer-events: auto;
pointer-events: none;
pointer-events: visiblePainted; /* SVG only */
pointer-events: visibleFill;    /* SVG only */
pointer-events: visibleStroke;  /* SVG only */
pointer-events: visible;        /* SVG only */
pointer-events: painted;        /* SVG only */
pointer-events: fill;           /* SVG only */
pointer-events: stroke;         /* SVG only */
pointer-events: all;            /* SVG only */

/* Global values */
pointer-events: inherit;
pointer-events: initial;
pointer-events: unset;

```
这里有一个特别的值，它进行了专门的介绍，也正是因为这个原因，水印的功能才得以实现。
> 除了指示该元素不是鼠标事件的目标之外，值none表示鼠标事件“穿透”该元素并且指定该元素“下面”的任何东西。

由上可知，当我们将pointer-events值设置为none时，它就有了穿透的特性，也就是说你“看得见它，却摸不到它”，而且也不具备鼠标指针的特性，这不正是水印想要的效果嘛，完美~;

接着往下看我们可以知道它的初始值是auto，并且可以继承
![Y0J0XD.png](https://s1.ax1x.com/2020/05/14/Y0J0XD.png)

水印功能就这样实现了是不是简单实用，那么是不是就没有缺点呢，当然要硬说有的话那就是兼容性的问题吧。

因为这个css3的属性是新出的，自然ie9及以下自然就不支持了，而且ie不支持的范围要到ie11以下，所以ie你为啥不去死呢。。。

![Y0YEjO.png](https://s1.ax1x.com/2020/05/14/Y0YEjO.png)
