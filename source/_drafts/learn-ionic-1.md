---
title: 从零开始：学习Ionic（一）
date: 2020-11-11
categories: 
  - 移动开发
tags: 
  - Ionic
---

> 作者：李旭光
> 引用请标明出处

## 前言

在移动 `App` 开发领域，有一个框架非常知名，每每面试都会被问起，“你用过 `Ionic` 么？”，是的，就是 `Ionic` 框架。

之前已经学习过了 `Cordova` ，它和 `Ionic` 可以说是一对黄金搭档，一个面向 `MobileOS` ，一个面向 `UI` ，很多人搞不清楚它们之间的区别，没关系，回头我再抽时间讲一下，今天我们主要来认识一下 `Ionic`。

好戏开始：）

<!-- more -->
## 一、 Ionic 简介
学一门技术，我们首先要知道它是谁，能干啥，有啥好处。要了解一门新技术最好的方式就是去它的官网看看。
（一言不合丢给你一个官网 ⇒ [官网](https://ionicframework.com/)）

> One codebase.
Any platform.
Now in Vue.

翻译过来是这样的

> 一份代码
任何平台
现在支持Vue

看着是不是眼熟，嗯，跟 `Cordova` 的口号何其相似，目标都是跨平台，只是 `Cordova` 跨的是 `MobileOS` 而 `Ionic` 跨的是前端框架。
最下面一句 `Now in Vue` 像是在说，“嗯，我也支持Vue了（Vue发展太快了，不能掉队）”，要知道，最开始的 `Ionic` 是妥妥的 `Angular` 派，不过无所谓，管他黑猫白猫，能完成任务就是好猫。

再往下看你会看到，巴拉巴拉说了一堆好处，比如开发迅速啊，组件优美且丰富啊，社区强大啊，还有跨平台（支持原生 `js` ，`Angular` ，`Vue` ，nice~），另外还提供了 `native` 的组件来直接调用移动设备的功能，可以说是相当强大了。

好了，看完了介绍，我们赶紧快速上手吧。
### 快速上手
在使用`Ionic`做开发还是要做一些准备工作，`node`，`git`都是必不可少的，所以请先行准备好。

目前，`Ionic`提供了两种创建工程的方式:
* 一种用官方的话说叫`StepByStep`，看过之后我理解就是通过命令的方式，一步步来；
* 另一种的话是官方提供了一个向导工具，就跟安装软件一样填一些必要的信息，然后一路下一步就生成了一个工程；

接下来我将使用这两种方式分别创建工程，首先是第一种。
#### StepByStep 创建工程
1. 全局安装`Ionic`的脚手架工具
``` js
npm install -g @ionic/cli
```
2. 创建工程
``` js
ionic start myApp tabs // myApp 是项目目录名称也是工程名称 tabs是模板名称
```
3. 启动工程
``` js
cd myApp
ionic serve
```

嗯，上面三个步骤之后，你的Ionic项目就启动起来了。（What ！？ 这么简单？！ 嗯，是的。）

#### 向导式（App Wizard）创建工程
这是一个在线创建工程的手段（感觉对于开发人员来说貌似还是命令行来的方便，这种可视化方案应该是给小白用户准备的吧），地址如下
> [https://ionicframework.com/start](https://ionicframework.com/start)

![此处是个图]()

点开上面的地址，你会看到如图所示的一个界面，我们可以看到它要求你输入的一些信息
1. App Name
2. Icon
3. Theme Color
4. Template
5. Framework

可以看到这比命令行要多出几个信息，上面的命令行只要求了 `App Name` 和 `Template` 难道强大的命令行不支持设置这些么？答案是否定的，当然可以设置这些信息，只是以参数的形式而已，先不说这个，填写好上面的信息以后我们点击`[create App]`按钮，这时我们进入了下一步，选择代码仓库（Choose a git host），你会看到下面有一个`[Skip]`按钮说明这步不是必须的，我们先选择一个`git`仓库，这里我选择`github`，其他的感兴趣的同学可以自己试试。点击`[connect]`，这时会弹出一个窗口，申请对应git仓库的鉴权认证，认证通过后我们点击`[choose]`，等待程序运行一会儿后工程就会被上传到git仓库中，并且会跳转到一个`DashBoard`，提供了可视化管理工程的页面（也太高大上了吧），右侧我们看到如何把代码下载到本地，并运行它，真的是很方便。

例：
``` js
npm install -g @ionic/cli cordova-res // cordova-res 指的是安装 cordova 的开发依赖，因为除 cordova 之外还可以选择如 phonegap 之类的MobileOS 框架
git clone https://github.com/lixuguang/li-app.git li-app
cd li-app && npm install && ionic serve
```
（待续...）

