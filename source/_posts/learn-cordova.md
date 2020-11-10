---
title: 从零开始：学习Cordova
date: 2020-11-10
categories: 
  - 移动开发
tags: 
  - Cordova
---

> 作者：李旭光
> 引用请标明出处

## 前言

很长时间没有写博客了，因为最近换了工作，原来的技术栈基本上都换了，现在接触了很多新的技术栈，Cordova就是其中之一。

接下来这篇文章就是我的学习笔记，以供回顾，写的不好请勿拍砖。

<!-- more -->
## 一、What is Cordova？ Cordova是个啥？
移动端开发和web开发有一些不一样，web端开发面向的是浏览器，而移动端开发面向的是各种移动设备，那么针对各种移动设备提供的SDK进行开发的话，我们通常称之为`原生开发`，原生开发虽然通常调用底层api，性能更好，但是因为不同移动终端底层技术不一样，所以每种设备都要单独开发，这样开发成本和能力要求都比较高，所以通常都会有一些框架来兼容各种平台，就好比java虚拟机可以运行在mac/windows/linux一样，为了能够写一套移动端代码可以在各种设备中使用，于是乎就有了Cordova，上面说了这么一堆废话，其实简言之，Cordova就是提供了跨设备调用底层Api的移动端开发框架（这种开发方式又叫做Hybird 模式）知道这一点应该就够了，当然这是我认为的。

Cordova的官网地址如下
> https://cordova.apache.org/

其实从这里就可以看出，Cordova是apache基金下的source，其官网是这样介绍它的
> Mobile apps with HTML, CSS & JS
Target multiple platforms with one code base
Free and open source

翻译成中文的话就是
> 使用Html，CSS和JS进行移动端应用开发
一份代码多平台使用
免费并且开源

三句话解释了Cordova的三个方面，
- 使用前端技术开发-对前端开发友好
- 跨平台-节省开发工时，降低开发难度-从图标看支持Android/ios/windowphone三大平台
- 免费不用解释开源的话意味着会有很多人贡献代码

以上奠定了Cordova强大及流行的基础。

## 二、快速上手
在使用Cordova做移动端开发之前需要做一些环境准备工作，因为它是使用nodejs开发的所以首先要安装node以及它携带的npm包管理器，这个现代的前端开发应该都知道，我这里就不多说了，装它就完了。

装好之后我们就要用npm全局安装一下Cordova项目的依赖，命令很简单；
方便的话还要装一下git，因为一些命令要在gitcmd上执行，当然管理代码也是需要它的，所以装吧。
``` js
npm install -g cordova
```
就这么简单就可以开发Cordova应用了，接下来我们要创建一个项目，创建项目的命令也十分简单。在命令行工具中输入下面的命令你就创建了一个Cordova的应用
``` js
cordova create <path> // 这里的path是项目的路径
```
如果要查看创建项目时还能设置什么参数你可以执行下面的命令
``` js
cordova help create
```
怎么样是不是超级简单。
做了上面的工作你就创建了一个具备基本功能的Cordova项目，接下来我们要给项目配置一些东西。

首先刚才讲到了，Cordova是一个跨平台的框架，那么你的项目要适配什么平台你需要进行一些配置，cd到项目目录下执行下面的命令
``` js
cordova platform add <platform name> // platform name 为适配平台的名称
```
如果不知道名称是什么命令行工具也贴心的给了查询用的命令，通过此命令你可以看到你安装了什么平台的适配，有哪些可以用
``` js
cordova platform

```
执行了这个命令你将看到下面这样的list
``` js
/***
Installed platforms:
Available platforms:
  android ^9.0.0
  browser ^6.0.0
  electron ^1.0.0
  windows ^7.0.0
*/
```
项目也创建了，简单的配置工作也完成了，我们可以启动项目看一下效果了，那么我们就该执行运行项目的命令了
``` js
cordova run <platform name> // platform name 适配平台的名称
```
到这我们就快速的创建并运行起了一个Cordova的工程。

### 小Tip
如上执行启动命令后，默认会启动8000端口，如果被占用的话可以通过增加命令行参数的形式改变端口号，参数如下设置
``` js
cordova run browser --target=chrome --port=9090
```
以上就可以自定义端口及浏览器启动应用了。
## 三、详细了解
### 1、概览
官网开头介绍了Cordova的身世和适用范围，前面开头已经讲过了不想再讲，直接看看Cordova的架构设计图。
[此处是个架构图]()
这个图理解起来不难，大的方面是两个部分，底层是MobileOS，也就是手机的操作系统本身，上层是由Cordova构建的框架；
Cordova内部的话是两层结构：上层的话是由前端代码构成的webapp部分；下层是Cordova的视图渲染引擎，它为webapp提供了HtmlApi和CordovaApi，也就是说我们可以调用html的原生api也可以使用cordova提供的jsApi；
Cordova还提供了丰富的插件系统；通过Cordova的视图引擎调用Cordova的nativeApi来调用一些Cordova提供好的调用设备的信息的Api，另外也可以调用一些用户自定义的Api；
Cordova视图引擎和插件系统再将上层的需求通过MobileOS开放的api能力实现具体功能，由此完成了整个Cordova框架的使用；

总的来说这个原理跟大多数跨平台的框架原理都类似，由框架提供统一的API能力，再由框架处理不同平台的兼容，这里特别之处在于渲染引擎可以允许使用前端原生的api，这将大大降低开发的难度。

#### WebView
说到移动端开发，不知道WebView应该是不可能的，在原生移动应用中，WebView就是移动应用内部嵌入的一个‘浏览器’，它可以允许你使用前端技术展示内容。
#### WebApp
WebApp跟WebView两个词有些像，事实上它们也确实有些关系，WebApp从名字就可以看出是使用Web技术开发的App，那么它跟WebView是什么关系呢，打个比方，WebView就是个快递盒子，WebApp就是你买的商品，一个是容器，一个是内容；这样说应该就明白了吧，通过前端技术开发的App通过WebView嵌入到App中，就可以带给用户原生应用的体验，忘了说WebApp是只能用浏览器访问的App；

和纯WebApp不同，嵌入到WebView中的WebApp需要有一个配置文件，`config.xml` ，它在项目的根目录下，说明了应用的一些信息，这个文件必不可少！！！
#### 插件系统
刚刚在上面的架构图中我们介绍了Cordova的插件系统，它提供了我们通过js调用MobileApi的能力，如电源，相机，联系人等等；官方维护了核心的功能，当然如果你愿意也可以调用一些第三方提供的插件，你可以方便的通过npm包管理器安装；
***但是有一点必须要说：项目创建后默认是不带任何插件的，即使是官方的核心插件也是需要你自己导入进去，第三方的更不用说了，另外Cordova只提供了功能Api并不包含任何的UI部件和MV框架（你可以根据自己喜欢使用Angular或者Vue或者其他什么都可以），这一点要牢记。
#### 开发工作流
使用Cordova开发MobileApp的时候即可以开发多个平台的app（一次开发多平台可用，降低开发成本），也可以专注开发某一平台的app（使用前端技术开发，降低开发难度），因此开发工作流也分为两种：
##### 跨平台(CLI)的工作流
官网上说了很多，也不难理解，这里用我自己的话说就是，Cordova提供了一个牛叉的CLI工具，它可以自动的完成一次编码多平台构建的事，你只需要按照它说的方式开发就行。：）
##### 平台为中心的工作流
这里不得不说我没有看很明白，但是结合下面的注意事项我是这样理解的，因为只为某一平台服务，所以有一些针对此平台的特殊的功能或者插件你就可以使用了（虽然不太清楚为什么这么做，如果你知道告诉我，谢谢），但是一旦使用了这种开发方式，那么就回不到跨平台开发了，因为你使用了针对某一平台才能调用的代码，这也不难理解，从字里行间感觉就是不建议使用这种开发工作流（好的好的，知道了）。

好的，接下来开发App啦；

### 2、开发应用
快速上手中已经介绍了如何准备、创建和启动应用，如何添加平台，这里就不多说了。说些没讲过的。
上面说过检查目前平台安装情况的话可以使用下面的命令
``` js
cordova platforms
```
我们也可以使用另一个命令来看，我试验了一下，效果是一样的。。。（问号脸，一样的为什么搞两个命令）
``` js
cordova platform ls
```
** 事后我发现，无论我使用的命令是 `platform` 还是 `platforms` 加不加 `ls` 都可以查到当前安装的平台。。。 啊~兼容性好强。

每执行一次 `add` 命令，你就会发现工程目录下的 `platforms` 目录下就对应生成了一个对应平台的文件夹，切勿手贱删除，你虽然删除了文件夹，但是各处设置的platform并没有去掉，会导致报错，如果要删除请使用 `remove` 命令。 想知道还有哪些命令的话就看下面的链接吧。⇒ [命令大全](http://cordova.axuer.com/docs/zh-cn/latest/reference/cordova-cli/index.html#cordova-platform-command)

### 安装构建的先决条件
因为Cordova的底层还是要调用MobileOS的接口，所以各平台的SDK是必须要装的，当然这里指的是你要进行构建的平台，比如你要构建Android应用，那么你就要装Android的SDK，ios同理。**这里有一个例外**`broswer`平台是不需要依赖其他SDK的（明明是开发APP，这里只是为了验证画面比较方便）
要看你是否满足了构建需要的依赖，可以执行下面的命令查看
``` js
cordova requirements
```
执行完后你会得到如下结果
``` js
Requirements check results for android:
Java JDK: installed .
Android SDK: installed
Android target: installed android-19,android-21,android-22,android-23,Google Inc.:Google APIs:19,Google Inc.:Google APIs (x86 System Image):19,Google Inc.:Google APIs:23
Gradle: installed

Requirements check results for ios:
Apple OS X: not installed
Cordova tooling for iOS requires Apple OS X
Error: Some of requirements check failed
```
这样你就可以清楚的知道自己需要安装哪些依赖才能完成对应平台的构建，简直方便的不要不要的。
具体想知道不同的平台都依赖些什么，你可以参见下面的链接；
* [Android平台的要求](http://cordova.axuer.com/docs/zh-cn/latest/guide/platforms/android/index.html#requirements-and-support)
* [iOS平台的要求](http://cordova.axuer.com/docs/zh-cn/latest/guide/platforms/ios/index.html#requirements-and-support)
* [Windows平台的要求](http://cordova.axuer.com/docs/zh-cn/latest/guide/platforms/win8/index.html#requirements-and-support)

### 构建App
在 `create` 项目之后，项目的根目录下会生成一个 `www` 目录，这个目录包含了 `webapp` 的入口页面 `index.html` ，入口文件的话是 `www/js/index.js` 文件的 `deviceready` 事件中。

如果要构建代码的话，执行下面的命令
``` js
cordova build
```
这个命令会构建你安装的所有平台，如果只想构建某一平台的话，可以把平台的名字加在命令后面，如
``` js
cordova build ios
```
如果想了解更多的参数的话，可以看一下后面的链接。⇒ [更多参数](http://cordova.axuer.com/docs/zh-cn/latest/reference/cordova-cli/index.html#cordova-build-command)

### 测试App
构建完app之后我们就可以测试了，通常各平台的SDK中会提供模拟器，我们只需要执行下面的命令就可以启动模拟器。
``` js
cordova emulate android
```
当然如果你觉得用实机查看更自然一些的话，也可以把手机插上数据线与电脑建立连接，然后执行下面的命令就可以了。
``` js
cordova run android
```
上面演示的是 Android 的app测试过程，每个平台虽然基本类似，但还是有出入，所以，下面提供了不同平台的调试方法，供你预览。
* [设置Android模拟器](http://cordova.axuer.com/docs/zh-cn/latest/guide/platforms/android/index.html#setting-up-an-emulator)
* [Cordova run 命令参考文档](http://cordova.axuer.com/docs/zh-cn/latest/reference/cordova-cli/index.html#cordova-run-command)
* [Cordova emulate 命令参考文档](http://cordova.axuer.com/docs/zh-cn/latest/reference/cordova-cli/index.html#cordova-emulate-command)

### 添加插件
（待续...）