---
title: CSS border三角、圆角图形生成技术详解
date: 2013-06-26 22:22:22
categories: 
  - 前端技术
	- CSS
tags: 
	- CSS技巧
---
> 作者：李旭光
> 引用请标明出处


# 前言

利用CSS的border属性可以生成一些图形，例如三角或是圆角。纯粹的CSS2的内容，没有兼容性的问题，我之前在纯CSS实现各类气球泡泡对话框效果一文中算是比较详细的讲述了CSS border属性生成三角的原理，以及实例。我觉得此技术相当实用的，故本文再次简单叙述一下，另外，本文还将展示可能并不为众人所知的CSS border圆角生成技术。好了，裹脚布的话就不说了，直接进入正题。
<!-- more -->

# CSS border生成三角技术简介
## 效果抢鲜
下图为使用CSS的border属性实现的三角效果：
```
// css 代码
.test{
  width: 0; 
  height: 0; 
  border-width: 20px 10px; 
  border-style: solid; 
  border-color: #ff3300 #ff3300 #ffffff #ffffff;
}
```
如何实现的，为何会有这样的效果，不急，take it easy!
## 梯形图案
看下面这段样式：
```
.test{
  width: 10px; 
  height: 10px; 
  border: 10px solid; 
  border-color: #ff3300 #0000ff #339966 #00ff00
}
```
当某个div应用了上面这个样式后，结果会如何？见下图（截自Firefox3.5，IE浏览器有细节上的差异）：
## 更进一步 – 部分边框透明
现在，设想一下，如果我们现在只保留一个一个上边框，其余边框均transparent透明（或与背景色同色），那么是不是就只显示一个上面红色的边框了，我们测试下，与上面类似的代码，只是修改下其余三个边框的颜色。
```
.test{
  width:10px; 
  height:10px; 
  border:10px solid; 
  border-color:#ff3300 #ffffff #ffffff #ffffff;
}
```
结果如下图（截自Firefox3.5）：
## 从梯形到三角
上面的是梯形，我要想得到一个三角图案该怎么办呢？显然，很简单，把div的高宽都变成0，只留一边，不就是三角了吗？如下代码：

```
.test{
  width: 0; 
  height: 0; 
  border: 10px solid; 
  border-color: #ff3300 #ffffff #ffffff #ffffff;}
```
结果如下（依旧截图自Firefox3.5）：
## 从等腰直角三角形到普通等腰三角
上图为等腰直角三角形，之所以为等腰直角，是因为所有的边框宽度是一样的，如果我们将边框宽度设置为不同，那会怎样？则会形成等腰三角形。如下代码：
```
.test{
  width: 0; 
  height: 0; 
  border-width: 20px 10px; 
  border-style: solid; 
  border-color: #ff3300 #ffffff #ffffff #ffffff;}
```
得到的结果如下图：
## 从等腰到不等腰
我们可以不局限于保留一条边框，我们可以保留两条，于是我们可以告别等腰，得到更加锐利的三角，正如一开始所展示的那个三角：
```
.test{
  width: 0; 
  height: 0; 
  border-width: 20px 10px; 
  border-style: solid; 
  border-color: #ff3300 #ff3300 #ffffff #ffffff;}
```
## 实际的应用
关于应用，不多说，直接看图：
说明：
以上的测试代码纯粹为了说明原理，所以使用#ffffff白色边框，通过于背景融合来隐藏边框。在实际的操作中，应该使用transparent透明属性，例如border-color:#ff3300 #ff3300 transparent transparent;，这同样会有问题，IE6浏览器不支持transparent透明属性，不过没有关系，就border生成三角技术而言，直接设置对应的透明边框的border-style属性为dotted或是dashed即可解决这一问题，为什么使用dotted和dashed可以修复此问题呢？您有兴趣可以参见默尘的这篇文章Dotted&Dashed终极分析及IE6透明边框。

# CSS border圆角生成技术简介
## 我看圆角
一提到圆角，我脑中闪过的词就是“定位”，“嵌套”，“模拟”，“渐进增强”，“滥用”。
- 定位，也就是切四个角上下左右定位，这是淘宝首页的做法，但是面对IE6的奇偶bug只能当作看客；
- 使用“嵌套”则不会有此问题，“嵌套”分图片背景嵌套和CSS边框嵌套，使用图片嵌套则图片的重用性，大小优化有待加强，边框嵌套则技术实现上有些难度；
- 或使用“渐进增强”，CSS3 border-radius属性，而不要去鸟IE这类自我感觉良好的浏览器；
- 或是学习Google使用CSS模拟，而一般的CSS模拟都是使用左右边框+背景色的方式1像素1像素的拼合成的。这类方法各有优缺点，需根据实际情况采用。对于满眼圆角的设计图我是很不喜欢的，该用则用，切勿为了圆角而圆角。

## border圆角图案生成法
这里介绍的实现圆角的得到与上面提到的都是不一样的，虽然也属于CSS模拟的范畴，但是其高效的程度确实相当惊人的，可谓最佳实践之一。
我们先看看效果，见下图，截自Firefox3.6：
上述效果的实现仅仅使用了三个标签，如下代码：
```
// html 代码
<div class="box">
  <div class="top"></div>
  <div class="center">我是一只小小鸟、小小鸟！</div>
  <div class="bot"></div>
</div>

// css 代码
.box{
  width:500px;
}
.top{
  border-bottom:3px solid; 
  border-top-color:#cc0000; 
  border-bottom-color:#cc0000; 
  border-left:3px dotted transparent; 
  border-right:3px dotted transparent;
}
.center{
  padding:10px 20px; 
  color:white; 
  font-size:14px; 
  background:#cc0000;
}
.bot{
  border-top:3px solid; 
  border-top-color:#cc0000; 
  border-bottom-color:#cccccc; 
  border-left:3px dotted transparent; 
  border-right:3px dotted transparent;
}
```
我们看看这段代码在IE6下的效果：

这里的高效在于，仅仅使用了一层标签就模拟了3像素的圆角，按照曾经我对CSS圆角模拟的理解，模拟1像素的圆角需要一层标签(background+borderLeft+borderRight)，两像素的需要两层标签，三像素的需要三层标签。

有点神奇，但是就像看刘谦的魔术一样，说穿了也就那么回事，其实这里的圆角模拟在本文的上面已经展示了，就是这样图片：

您可能会疑问，是不是搞错图片啦，这显然不是一个模样的，非也非也，就本质上而言，圆角的实现与上面的梯形图就是同样的东西。现在，盯着上面这张图，我们想象一下，用力的想象，用想花姑娘的那番劲头想象——上面的梯形宽度越来越宽（不是拉伸），一直宽到500像素，是不是与上面实现的圆角的下边缘一致啊？

也就是说，那个含有“我是一只小小鸟……”文字的圆角图形是有一个上梯形+矩形+下梯形组成的。参见下面的分离效果图：
您可以狠狠地点击这里：CSS border圆角生成demo

## 局限性
人无完人，金无足赤，此方法虽然简洁高效，兼容性上佳，但是依然有局限性，在实现实色背景的圆角效果时，此方法可谓首选；如果是纯粹的圆角边框，此方法也可以实现，需要用到边框重叠，但是标签数几乎要翻倍，其权衡效用将大打折扣，反不如其他圆角方法来的实在。

# 结语
如果在web制作中，需要用的一些直接可以使用CSS+单标签模拟的图片，我的建议是“毫不犹豫使用CSS模拟”，例如实色的三角，或是实现实色的圆角效果，这可以说是最高效，最利于扩展维护的前端实现方法了。我们需要开阔的思维，而不要仅仅局限于眼前的技术，武侠中所谓的“无招胜有招”还是有着一定的哲学道理的，长远来看，意识与海纳百川的心态比当下的一点技术更来得重要。
