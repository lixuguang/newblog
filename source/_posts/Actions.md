---
title: 巧妙利用Acitons进行博客的自动构建
date: 2019-12-27 22:22:22
categories: 
	- 博客技巧
tags: 
	- Github
	- Actions
	- Hexo
---
> 作者：李旭光
> 引用请标明出处


# 前言

忙活了很久很久，终于不那么忙了，也终于想起来我还有个博客没有运行，哎，虽然用Hexo可以享受Github提供的免费的空间和域名，但是命令行敲代码的方式实现博客编写还是有些不方便，尤其是需要编程环境的时候，最近公司在用CICD的方式对代码进行构建，我就在想有没有一种方式让博客也可以变成这样，于是我就在网上搜`Github` `Hexo` `自动构建` 这些个词，还真让我搜到了一种方法而且还是Github自己提供的，下面隆重请出 `GitHub Actions`。
<!-- more -->

## GitHub Actions 是什么
GitHub Actions 由 GitHub 官方推出的工作流工具。典型的应用场景应该是 CI/CD，类似 Travis 的用法。如果不知道 CI/CD&Travis 感兴趣的建议去了解一下，下面不展开说明，直接说怎么用就好。

## 前期准备
在使用 `GitHub Actions` 之前我们先来看看我们有什么；
首先我们有一个放博客程序的地方，我这里是叫做 `blog-source` ，另外呢有一个通过 hexo g 创建出来的静态网站，为了存放它而建的另一个仓库，我这里是叫做`lixuguang.github.io`,也就是说我们现在是有这样两个仓库。
|仓库|作用|
|-|-|
|blog-source|放博客源代码|
|lixuguang.github.io|放博客生成代码|

## 生成密钥
因为 `GitHub Actions` 它需要访问我的 `blog-source` 仓库的代码所以必须要有密钥，密钥大家应该熟悉了，创建博客的时候也是创建了一个公钥和私钥用来在本地往 `lixuguang.github.io` 这个仓库提交代码
这里呢我们用下面的命令生成密钥。
```
ssh-keygen -t ed25519 -f ~/.ssh/github-actions-deploy # 连按三次回车即刻
```
命令执行完成后，我们会得到两个文件 `github-actions-deploy` 和 `github-actions-deploy.pub` 两个文件，第一个是私钥，第二个是公钥。
|名称|解释|
|-|-|
|github-actions-deploy|私钥|
|github-actions-deploy.pub|公钥|

接下来的步骤一定要好好看，因为我在这个地方被卡住好多次，就是因为有的文章说的并不正确，或者至少是讲的不够仔细，这里我会仔细地说明一下。

## 配置 GitHub 仓库
### 配置博客源代码仓库
我这里的源代码是放在 `blog-source` 中，所以我现在要给源代码仓库配置`私钥`，配置过程如下：
打开 `blog-source` 仓库，选择 `settings`，然后选中 `secrets` ， 再点击 `Add new secrets`，照着下面填写内容
|字段|值|
|-|-|
|Name|HEXO_DEPLOY_PRI（名称自动构建时有用）|
|Value|github-actions-deploy|

### 配置博客源代码仓库
我这里生成的博客静态代码是放在 `lixuguang.github.io` 中，所以我现在要给静态代码仓库配置`公钥`，配置过程如下：
打开 `lixuguang.github.io`，选择 `settings`，然后选中 `keys`，再点击 `Add deploy key`，照着下面填写内容
|字段|值|
|-|-|
|Title|HEXO_DEPLOY_PUB|
|Key|github-actions-deploy.pub|

## 编写 Actions 脚本

经过上面一系列的准备操作，终于来到了编写自动构建脚本的环节，构建脚本如下，如果按照上面我做的操作一步步来的话，那么这一步你可以直接copy啦
```
name: Deploy Blog

on: [push] # 当有新push时运行

jobs:
  build: # 一项叫做build的任务

    runs-on: ubuntu-latest # 在最新版的Ubuntu系统下运行
    
    steps:
    - name: Checkout # 将仓库内master分支的内容下载到工作目录
      uses: actions/checkout@v1 # 脚本来自 https://github.com/actions/checkout
      
    - name: Use Node.js 10.x # 配置Node环境
      uses: actions/setup-node@v1 # 配置脚本来自 https://github.com/actions/setup-node
      with:
        node-version: "10.x"
    
    - name: Setup Hexo env
      env:
        HEXO_DEPLOY_PRI: ${{ secrets.HEXO_DEPLOY_PRI }} # 这里是上面配置的私钥名称
      run: |
        # set up private key for deploy
        mkdir -p ~/.ssh/
        echo "$HEXO_DEPLOY_PRI" | tr -d '\r' > ~/.ssh/id_rsa # 配置秘钥
        chmod 600 ~/.ssh/id_rsa
        ssh-keyscan github.com >> ~/.ssh/known_hosts
        # set git infomation
        git config --global user.name 'lixuguang' # 换成你自己的名字
        git config --global user.email 'lixuguang@gmail.com' # 换成你自己的邮箱
        # install dependencies
        npm i -g hexo-cli # 安装hexo
        npm i
  
    - name: Deploy
      run: |
        # publish
        rm -rf .deploy_git # 如果上次构建失败这句命令会清除上次失败的代码
        hexo generate && hexo deploy # 执行部署程序


```

---

通过以上这些步骤的操作，如果没什么意外的话，博客的自动构建就完成了，之后只要你提交新的文章到博客源代码仓库，它将自动帮你生成并发送到博客的静态代码仓库，再也不用执行hexo g -d啦，如果这篇文章对你有用，欢迎follow我或打赏一下这篇文章，感谢阅读。

ps：这里有个小坑需要注意一下，因为博客的皮肤也是另外一个git仓库，如果你在本地构建好用但是线上构建博客不显示了，需要注意下是不是皮肤没有上传到博客源码仓库，这里我遇到了，希望你不会因此困扰，拜拜~