---
title: 前端常见知识点整理 ---- 柯里化 currying
date: 2020-01-02 20:29:22
categories: 
	- 前端技术
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处


# 定义 
柯里化是一种将使用多个参数的一个函数转换成一系列使用一个参数的函数的技术。

柯里化，可以理解为***提前接收部分参数，延迟执行，不立即输出结果，而是返回一个接受剩余参数的函数***。因为这样的特性，也被称为部分计算函数。

通俗易懂的解释：用***闭包***把参数保存起来，当参数的数量足够执行函数了，就开始执行函数。柯里化，是一个逐步接收参数的过程。在接下来的剖析中，你会深刻体会到这一点。

```
// js代码

function currying(fn){
    var allArgs = [];

    return function next(){
        var args = [].slice.call(arguments); // 拆成数组元素

        if(args.length > 0){
            allArgs = allArgs.concat(args);
            return next;
        }else{
            return fn.apply(null, allArgs);
        }
    } 
}
```
我们来一个简单的实例验证一下：

```
// js代码

var add = currying(function(){
    var sum = 0;
    for(var i = 0; i < arguments.length; i++){
        sum += arguments[i];
    }
    return sum;
});

add(1)(2, 3)(4)() // => 10
```

# 应用场景
## 参数复用
```
// js代码

function getUrl(domain, protocol, path) {
	return protocol + "://" + domain + "/" + path;
}

var page1 = getUrl('http', 'lixuguang.github.io', 'page1.html');
var page2 = getUrl('http', 'lixuguang.github.io', 'page2.html');
```
我们使用currying来简化它：

```
var conardliSite = currying(getUrl)
var page1 = conardliSite('page1.html')('http', 'lixuguang.github.io')(); 
```