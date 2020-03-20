---
title: 如何利用GitHub-Hexo-Next搭建一个漂亮的技术博客
date: 2017-10-11 16:18:48
categories: 
  - 博客技巧
tags: 
  - Hexo
---

> 作者：李旭光
> 引用请标明出处

## 前言
> 开博客貌似并不是一件难事，现在有很多的方式开博客，而且现在博客貌似也不再流行，但是作为一个开发者，维护一个技术博客对自己的发展是很有好处的，出名者如阮一峰等，所以还是想要做一个技术博客的。

> 开博客可以选择网上现有的博客系统如博客园以及其他各大公司提供的博客系统，也可以选择如wordpress等博客程序自行搭建博客系统，前者省事，但可自定义设置不足不符合一个爱折腾的人的性格，后者复杂些，爱折腾的可以学着搭建，但是服务器域名是个让人头疼的事，还有数据库等操作，着实麻烦，如今又有了另外一个选择，那就是利用GitHub和一些静态的博客系统搭建一个纯静态的博客系统，不花钱又能折腾，而且还是命令行下的，装B感觉棒棒的，另外搭配时下流行的MarkDown语法，做笔记正合我意。

<!-- more -->

## 技术栈选型
> 这里说是技术栈选型可能并不是很恰当，但又找不到合适的描述，就是把需要的技术介绍一下，如果还不会的，可以自行学习，或者看看我的其他文章。
- node（npm），现在node这么火，没用过都不好意思出门，但是如果你还不回的话，就先自行学习安装一下吧。
- Hexo 静态博客程序，其实还有很多，只不过这个比较新，而且搭配Next非常漂亮，就选了它。
- Next 可以说是Hexo的定制系统，不仅仅是做了个皮肤，简洁美观的配置项和官网说明深得我心。

## 搭建步骤（安装步骤）
### 安装Hexo [Hexo官网](https://hexo.io/)

```
$ npm install hexo-cli -g // 安装hexo的脚手架工具
$ hexo init blog // 初始化博客
$ cd blog // 返回博客目录
$ npm install // 安装依赖
$ hexo server // 启动博客
```
> 怎么样五行代码就生成并运行了一个博客是不是超简单。
下面我们看一下生成的博客的目录

```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```
`_config.yml`这是博客的配置文件，比如博客名称，副标题，作者等信息都在这个文件里设置。

`package.json`这是博客的依赖文件可以忽略

`scaffolds`这是博客的模板目录，当你要写一篇文章时，这里会有文章的默认类型。

`source`这是博客的网站资源，包括发布的文章（`_posts`）、关于、分类还有上传文件等。

`themes`这是博客的皮肤。

更多配置信息请查阅官网手册

### 安装Next主题 [Next官网](http://theme-next.iissnan.com/)


```
$ cd your-hexo-site
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

然后到`_config.yml`配置文件将主题配置改成next就可以使用next的皮肤了

```
theme: next
```
皮肤也有配置文件，为跟Hexo进行区分Hexo的配置文件称为`站点配置文件`， 皮肤配置文件称为`主题配置文件`。

对两个配置文件进行简单配置后，符合需求的博客就搭建而成了，这里有个友好的建议，配置文件如果配置不正确将不能正确运行博客，所以在配置前务必保留好原始配置文件，注意配置时不要缺了空格，不要问我为什么知道这个。

### 更新博客主题
https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/UPDATE-FROM-5.1.X.md

## 写博客
博客已经搭建好了，接下来就是写博客了，那么如何开始写博客呢，超级简单一行命令足以：

```
hexo new [layout] <title>  
// layout 为模板类型可以省略，title为文章标题，通常可以简写为如下
hexo new title

```
新建命令执行后在`_posts`的目录下就会生成一个你刚才命名的md后缀的文件，这就是一个MarkDown语法的文件，（如果不了解MarkDown语法的可以去学一下，很简单的符号语言，或者像我一样用支持MarkDown语法的编辑器来写文章。新建的文章打开内容如下

```
---
title: new-post
date: 2017-10-11 15:01:09
tags:
---

```
非常好理解，title就是标题，date为创建时间，tags是标签方便分类，但是这些并不全，还有些常用的分类没有写上，下面我将常用的进行补充

```
---
title: new-post
date: 2017-10-11 15:01:09
categories: 
	- node
	- npm
tags:
	- npm
	- node
	- rnpm
---
```
这样补充后就有了常见博客的分类和标签的功能，是不是很简单。

写完文章以后还要执行下面命令，生成静态页面

```
$ hexo generate // 将md后缀文件生成成静态html文件

```


这样我们就完成了博客的搭建和博客的书写，到现在我们就已经有了一个本地的博客，那么如何将博客上传到GitHub上呢？
## 将Blog上传至GitHub
github是一个代码托管的平台，为了方便描述代码功能，它提供了README.md文件进行说明，但是为了更好的展现，也提供了gitpage的功能，博客是基于这个功能进行的扩展，那么如何用gitpage的功能来实现博客系统呢？
### 创建仓库
创建一个以你的GitHub账号为开头命名的仓库，格式如下

```
GitHub账号名称.github.io
// 如
lixuguang.github.io
```
然后到blog系统的配置文件`_config.yml`里配置一下上传路径

```
deploy:
  type: git
  repo: <repository url>
  
// 我的实例
deploy:
  type: git
  repo: git@github.com:lixuguang/lixuguang.github.io.git
  branch: master

```
配置好就可以进行部署了，部署也很简单，只需要执行一下下面的命令。

```
$ npm install hexo-deployer-git --save // 安装上传工具

$ hexo deploy

```
稍等一会，如果没有出现什么错误信息，那么你的部署就成功了。之后你就可以访问你的博客了，博客地址如下：
https://你的github账号.github.io/
我的如下：
https://lixuguang.github.io/

现在你是不是已经学会如何利用github搭建一个静态的博客系统了呢，如果你还没有一个自己的技术博客，快来试试吧。

## 技巧
是不是觉得命令行还是挺麻烦的，要敲那么一大串字母，哈哈实际上这些常用命令是有缩写方式的，下面给大家介绍一下缩写方式。


```
$ hexo server
// 简写
$ hexo s

$ hexo generate
// 简写
$ hexo g

$ hexo deploy
// 简写
$ hexo d

$ hexo new
// 简写
$ hexo n
```

另外每次发布之前最好执行以下命令，清理当前内容

```
$ hexo clean

```
以防出现冲突的情况，具体动作如下

```
$ hexo clean
$ hexo g -d // 文件生成后立即部署网站
$ hexo d -g // 部署之前预先生成静态文件

```

## 常见问题
1. SSH问题

```
$ ssh-keygen -t rsa -C "lixuguang316@gmail.com"
// 填写你自己的github邮箱

```
敲三下回车，之后会在

```
C:\Users\Administrator\.ssh // windows下
open ~/.ssh // Mac下打开ssh文件  
```
文件夹下生成两个文件`id_rsa`（私钥）、`id_rsa.pub`（公钥），在github上的SSH处添加新的ssh，然后将`公钥`内容贴到上面起个名字可以叫hexo，保存，然后在git bash下敲击

```
$ ssh git@github.com
```
然后敲`yes`就可以上传blog代码了
怎么样会了么？更多高阶玩法请阅读官方说明文档，文章如有谬误之处请各位指出，如果觉得文章对你有所帮助我将十分开心，如果你喜欢我的文章可以到我的github上点个`fork`，谢谢你的阅读。
