---
title: path.join 与 path.resolve 的区别
date: 2020-03-23 14:42:52
categories: 
	- Nodejs
tags: 
	- Nodejs
---

# path.join 与 path.resolve 的区别

1. 对于以/开始的路径片段，path.join只是简单的将该路径片段进行拼接，而path.resolve将以/开始的路径片段作为根目录，在此之前的路径将会被丢弃，就像是在terminal中使用cd命令一样。 

``` js
path.join('/a', '/b') // 'a/b'
path.resolve('/a', '/b') // '/b'
```
2. path.resolve总是返回一个以相对于当前的工作目录（working directory）的绝对路径。


``` js
path.join('./a', './b') // 'a/b'
path.resolve('./a', './b') // '/Users/username/Projects/webpack-demo/a/b'
```
