---
title: webpack中loader和plugin之间的区别
date: 2020-01-22 18:38:00
categories: 
	- 前端技术
	- Webpack
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处

# 前言
最近在学习Webpack相关的知识的时候对loader和plugin有点迷惑，两个特性都是用来做打包相关处理，那么他们有什么区别呢，为了弄清楚他们有什么区别，我开始了疯狂的查找资料，虽说每篇文章都说了一些自己的认识，但是并没有一个特别标准或者说容易理解的答案，我且先将它们记录下来，以便日后回顾之时可以有一些感触。那么接下来我将开始webpack的解迷之旅。

<!-- more -->

# 背景知识
在研究loader和plugin之前区别之前，我们先来看看一个webpack配置的常见结构
``` js
// js代码

const webpack = require("webpack");
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
module.exports = {
  // 入口文件
  entry: {
    app: path.join(__dirname, "../src/js/index.js")
  },
  // 输出文件
  output: {
    filename: "[name].bundle.js",
    path: path.resolve(__dirname, "dist"),
    publicPath: "/"
  },
  // loader配置
  module: {
    rules: [
      {
        test: /\.scss/,
        use: [
          "style-loader",
          "css-loader"
        ]
      }
      ......
    ]
  },
  // plugins配置
  plugins: [
    // 重新创建html文件
    new HtmlWebpackPlugin({
      title: "首页",
      filename: "index.html",
      template: path.resolve(__dirname, "../src/index.html")
    })
    ......
  ]
}
```
***webpack的打包原理***

- 识别入口文件
- 通过逐层识别模块依赖(Commonjs、amd或者es6的import，webpack都会对其进行分析，来获取代码的依赖)
- webpack做的就是分析代码，转换代码，编译代码，输出代码
- 最终形成打包后的代码

## 什么是loader
我们可以看到loader实际上是在module的rules下，用对象的方式表示了需要处理的文件类型，和需要用哪些loader做处理

> loader是文件加载器，能够加载资源文件，并对这些文件进行一些处理，诸如编译、压缩等，最终一起打包到指定的文件中。

- 处理一个文件可以使用多个loader，loader的执行顺序和配置中的顺序是相反的，即最后一个loader最先执行，第一个loader最后执行
- 第一个执行的loader接收源文件内容作为参数，其它loader接收前一个执行的loader的返回值作为参数，最后执行的loader会返回此模块的JavaScript源码

## 什么是plugin
> 在webpack运行的生命周期中会广播出许多事件，plugin可以监听这些事件，在合适的时机通过webpack提供的API改变输出结果。

## loader和plugin的区别

对于loader，它是一个转换器，将A文件进行编译形成B文件，这里操作的是文件，比如将A.scss转换为A.css，单纯的**文件转换过程**

**plugin是一个扩展器，它丰富了webpack本身，针对是loader结束后，webpack打包的整个过程，它并不直接操作文件，而是基于事件机制工作，会监听webpack打包过程中的某些节点，执行广泛的任务**

下面我们来看一个例子：
``` js
// js代码

class MyPlugin{
  constructor(options){
    console.log("MyPlugin constructor:", options);
  }
  apply(compiler){
    compiler.plugin("compilation", compilation => {
      console.log("MyPlugin");
    });
  }
}
module.exports = MyPlugin;
 
// webpack.config.js配置：
module.exports = {
  ...
  plugins: [
    new MyPlugin({param: "my plugin"})
  ]
}
```

使用该plugin后，执行的顺序：

1. webpack启动后，在读取配置的过程中会执行new MyPlugin(options)初始化一个MyPlugin获取其实例
2. 在初始化compiler对象后，就会通过compiler.plugin(事件名称，回调函数)监听到webpack广播出来的事件
3. 并且可以通过compiler对象去操作webpack