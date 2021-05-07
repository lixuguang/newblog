---
title: Vue基础
date: 2017-08-30 17:03:05
categories: 
  - 前端框架
	- Vue
tags: 
	- Vue
---
> 作者：李旭光
> 引用请标明出处

# 前言
最近公司技术选型选择了VUE，下面就记录一下在学习Vue中遇到的一些知识点，有些在官方手册里已经讲的很详细了，这里就简单带过，主要对cli产生的以及自行添加的一些项目目录进行一下解释，希望对正在学习Vue的同学提供一点帮助，如果本文有谬误之处还请各位指出，谢谢各位支持。

<!-- more -->
### 搭建项目

```
npm install --global vue-cli
vue init webpack my-project
cd my-project
npm install（推荐用cnpm install）
如果没有cnpm ，先安装cnpm镜像
npm install -g cnpm --registry=https://registry.npm.taobao.org
npm run dev
```
### 目录讲解
> - build和config ：项目开发和打包时候的相关配置；
> - node_modules ：项目所需要的依赖文件；
> - src ：主应用/页面相关文件；
>    - assets ： 静态资源文件；
>    - components ：组件；
>    - res：资源
>      - css： 公共css或是css预处理文件；
>      - js：   公共js文件
>      - img：公共图片
>    - router ：路由配置文件；
>    - views ： 视图文件，其实也是vue组件。按照业务功能划分模块；
>    - vuex : 状态管理的配置文件；
>    - App.vue : 主组件；
>    - main.js： 入口文件，初始化vue实例并使用需要的插件
> - index.html ： 主html页面；
> - dist：webpack打包生成的文件；
> - package.json：记录依赖相关信息

### 文件的加载顺序:
当我们执行命令 `npm run dev`的时候根据配置文件`dev-server.js`里的相关配置去加载`webpack`的相关配置文件 在`webpack.base.conf`里面`entry`入口文件就配置了`app：'./src/main.js'` 

所以当我们在运行`npm run dev`的时候就开始通过`main.js`执行了。`main.js` 初始化vue实例并且加载相关配置插件，然后通过`app.vue`文件去访问各个组件

### Build/dev-server.js主要完成以下几件事情：
1. 检查node和npm的版本；
1. 引入相关插件和配置；
1. 创建express服务器和webpack编译器；
1. 配置开发中间件（webpack-dev-middleware）和热重载中间件（webpack-hot-middleware）；
1. 挂载代理服务和中间件；
1. 配置静态资源；
1. 启动服务器监听特定端口（8080）；
1. 自动打开浏览器并打开特定网址（localhost:8080）；

### Build/huild.js主要完成以下几件事情：
1. loading动画；
1. 删除创建目标文件夹；
1. webpack编译；
1. 输出信息

### 配置文件
#### .babelrc 
设置转码的规则和插件（使用es6语法必须安装插件）

```
npm install babel-preset-es2015
```

presets 字段是用来设定转码规则;

#### .editorconfig 
配置文件编码格式的文件
- indent_style:  设置缩进风格，tab或者空格;
- indent_size:  缩进的宽度;
- tab_width:   设置tab的列数。默认是indent_size;
- end_of_line： 换行符，lf、cr和crlf;
- charset：  编码;
- trim_trailing_whitespace： 设为true表示会除去换行行首的任意空白字符;
- insert_final_newline:   设为true表明使文件以一个空白行结尾;
- root: 表明是最顶层的配置文件，发现设为true时，才会停止查找.editorconfig文件;

#### .eslintignore  
忽略不符合eslint规范的文件, （一般会忽略掉第三方引用的插件）

#### .eslintrc.js 
定义代码规则

#### .gitignore  
配置文件，用于配置不需要加入版本管理的文件

### .VUE 文件解释
- template： 展示模板
- import ： 导入组件已经js文件
- export default： 
  - data：数据源；
  - methods：方法； 		       
  - mounted：页面加载之后执行的方法；	
  - created：页面生成时加载的方法；
- style：  样式代码  其中scoped表示样式作用范围为本vue文件



### 网络访问
#### axios
1. 发送请求：

```
axios#request(config)；
axios#get(url[, config])；
axios#delete(url[, config])；
axios#head(url[, config])；
axios#post(url[, data[, config]])；
axios#put(url[, data[, config]])；
axios#patch(url[, data[, config]])；
```

2. 处理响应：
- Promise语法；
- 处理结果：then；
- 处理异常：catch；
3. 拦截器（use/reject）：

```
axios.interceptors.response.use；
axios.interceptors.rquest.use；
reject(移除请求拦截)
```

4. 参数：
- json（默认）；
- qs；

### 组件通信
- Prpos：父组件对子组件；
- 自定义事件：子组件对父组件；
- 消息总线：任意两个组件；
- 状态管理：Vuex（适用于大型单页面开发）

### 路由

1. 配置

```
Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Hello',
      component: Hello
    }
  ]
})

new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: { App }
})
```

2. 导航
- push
- replace
- go
3. 参数传递
- RESTful url参数
- 参数查询 query
- 锚点  hash: '#data'
4. 嵌套路由
- Children
5. 钩子
- beforeRouteEnter
- beforeRouteLeave

### 状态管理
> Vuex是什么？
> 
> Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

- state里面就是存放的我们所要用到的状态；
- mutations就是存放如何更改状态的方法 ，同步操作；
- getters就是从state中派生出状态，比如将state中的某个状态进行过滤然后获取新的状态。
- actions就是mutation的加强版，它可以通过commit
- mutations中的方法来改变状态，最重要的是它可以进行异步操作。 
- modules顾名思义，就是当用这个容器来装这些状态还是显得混乱的时候，我们就可以把容器分成几块，把状态和管理规则分类来装。这和我们创建js模块是一个目的，让代码结构更清晰。
