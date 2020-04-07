---
title: Webpack 打包时利用 UglifyJsPlugin 去掉comments 、console 、和 debugger
date: 2020-04-07 11:22:00
categories: 
	- webpack
tags: 
	- UglifyJsPlugin
---
> 作者：李旭光
> 引用请标明出处


# 前言

最近在检查公司前端代码质量时，经常会在sonar检查到有漏洞级别的问题特别多，但是打开查看详情的时候发现又特别低级，都是些console没去掉啊，debugger没去掉啊，之前都是看到了就提醒一下，但是屡禁不止，那么有没有什么好的办法能够从源头上避免这个问题呢，果然这个问题不止我一个人遇到，网上还是有大神给出了解决方案，那就是 webpack 打包时可以引入 UglifyJsPlugin 来解决这个扰人的问题，而且屏蔽了人为的因素，使用感觉是极好的，所以，写个帖子把网上学来的资料整理一下。
<!-- more -->

## UglifyJsPlugin
关于 UglifyJsPlugin 的介绍，在 webpack 的官网上有这样一段描述 
> ℹ️ webpack =< v3.0.0 currently contains v0.4.6 of this plugin under webpack.optimize.UglifyJsPlugin as an alias. For usage of the latest version (v1.0.0), please follow the instructions below. Aliasing v1.0.0 as webpack.optimize.UglifyJsPlugin is scheduled for webpack v4.0.0

简单来说就是在 webpack3.0 之前，引入了这个插件的0.4.6版本，并用 webpack.optimize.UglifyJsPlugin 作为它的别名， 在 webpack4.0 中引入了插件的 1.0.0 版本，用了同样的名称作为别名，用的时候请注意。

那么“这个插件”是什么呢， webpack 的官网也告诉我们了，那就是![UglifyJS](https://github.com/mishoo/UglifyJS2/tree/harmony)

> A JavaScript parser, mangler/compressor and beautifier toolkit for ES6+.

翻译过来就是“一个用于ES6+的JavaScript解析器、（榨汁机：翻译的挺有意思）/压缩机和美化工具。”

再简单点说 uglifyJsPlugin 用来对js文件进行压缩，减小js文件的大小。***其会拖慢webpack的编译速度，建议开发环境时关闭，生产环境再将其打开。***

要用它的的话记得先安装
``` js
npm i -D uglifyjs-webpack-plugin
```
安装完成就可以用了
``` js
// webpack.config.js
const UglifyJsPlugin = require('uglifyjs-webpack-plugin')

module.exports = {
  plugins: [
    new UglifyJsPlugin()
  ]
}
```

当然他有很多的选项，具体想了解可以去 webpack 官网，或者去插件官网都可以找到![uglifyjs-webpack-plugin](https://www.webpackjs.com/plugins/uglifyjs-webpack-plugin/)

当然知道你们懒的去看了，心里肯定也在说“直接给我个现成的配置他不香么？”，别急嘛，这就给你们
``` js
new UglifyJsPlugin({
    //删除注释
    output:{
        comments:false
    },
    //删除console 和 debugger  删除警告
    compress:{
        warnings:false,
        drop_debugger:true,
        drop_console:true
    }
})
```
当然版本间可能会有些差别，但是option是不变的，有调整的话各位自行调整一下。
公司用的 vue-cli3 所以这里再给出个 vue-cli3 默认配置文件下的写法

``` js
// vue.config.js

configureWebpack:{
    optimization: {
        minimizer: [
            new UglifyJsPlugin({
                uglifyOptions: {
                    // 删除注释
                    output:{
                      comments:false
                    },
                    // 删除console debugger 删除警告
                    compress: {
                      warnings: false,
                      drop_console: true,//console
                      drop_debugger: false,
                      pure_funcs: ['console.log']//移除console
                    }
                }
            })
        ]
    }
}
```

## 问题收集
1. 运行出现报错 UglifyJs

> Q: DefaultsError: warnings is not a supported option

> A: 降低版本(使用 "uglifyjs-webpack-plugin": "^1.1.1")，打包正常，效果达到