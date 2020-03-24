---
title: 自己动手实现系列 ---- Array.prototype.flat()函数
date: 2020-03-24 12:40:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- 自己动手实现系列
---
# 原理
将多层数组扁平化

# 实现
``` js
Array.prototype.myFlat = function() {
  var arr = [];
  this.forEach((item)=>{
    if(Array.isArray(item)){
      arr = arr.concat(item.myFlat()); // 如果是数组的话继续循环
    }else{
      arr.push(item)
    }
  })
  return arr
};
```

还有另外一种实现方式，非常好用
``` js
Array.prototype.myFlat = function() {
	return this.toString() // => "1,2,3,4"
			.split(",")   		// => ["1", "2", "3", "4"]
			.map(item => +item); // => [1, 2, 3, 4]
};
```