---
title: npx是什么
date: 2020-01-22
categories: 
  - node
tags: 
  - npx
---

> 作者：李旭光
> 引用请标明出处

## 前言
最近在阅读一些文章时，经常看到介绍命令的时候用到了 npx 关键字，之前知道有 node 有 npm 但是很少用到 npx ，npx是个啥呢？好奇上网上找了些资料学习了一下，写篇学习笔记记录一下。

<!-- more -->
## npx 起源
我从阮一峰的博客中看到介绍 npx 的文章，开头的一句话说明了他诞生的日子。
> npm 从5.2版开始，增加了 npx 命令。

为了验证阮一峰这里介绍的正确性我特意下了对应的npm版本验证了一下确实如此，而且在网上找到了另一位大佬司徒正美（大佬走好）博客中也对 npx 做了介绍
> 最近我在更新 npm 5.2.0 的时候发现会买一送一，自动安装了 npx。

由此，我可以肯定的告诉大家，npx是npm在5.2.0之后版本推出的一个工具，那么他是干嘛用的呢？

## npx 作用
想要了解一个技术，最好的途经是他的官网，于是我到网上找到了 npx 在 github 上的仓库，地址如下
[npx仓库](https://github.com/npm/npx#readme)，其中对 npx 有这样一段介绍
> DESCRIPTION
> Executes <command> either from a local node_modules/.bin, or from a central cache, installing any packages needed in order for <command> to run.
> By default, npx will check whether <command> exists in $PATH, or in the local project binaries, and execute that. If <command> is not found, it will be installed prior to execution.
> Unless a --package option is specified, npx will try to guess the name of the binary to invoke depending on the specifier provided. All package specifiers understood by npm may be used with npx, including git specifiers, remote tarballs, local directories, or scoped packages.
> If a full specifier is included, or if --package is used, npx will always use a freshly-installed, temporary version of the package. This can also be forced with the --ignore-existing flag.

上面这一大段英文我想大家一定看了就头疼，所以为了大家不那么头疼，可以看一下下面我翻译的内容，如果有翻译不对的地方，还请指正。

> 解释
> 执行 command 命令，无论从本地（我理解为项目目录）node_modules/.bin 或者从全局缓存中， 安装所需执行的任何包。
> 默认情况下，npx将检查 command 是否存在于 $PATH 中，或者在本地项目二进制文件中，并执行该命令。
如果没有找到 command ，它将在执行之前安装。
> 除非指定了 —package 选项，否则npx将根据提供的说明符猜测要调用的二进制文件的名称。
npm可以理解的所有包说明符都可以与npx一起使用，包括git说明符、远程tarball、本地目录或作用域包。
> 如果包含完整的说明符，或者使用 ——package 选项，npx将始终使用新安装的包的临时版本。
这也可以用 ——ignore-existing 标记强制执行。

上面这段机翻简直让人无法理解，所以我又去大佬博客下看了下他们的解释

> npx 想要解决的主要问题，就是调用项目内部安装的模块。 -- 阮一峰
> 根据 zkat/npx 的描述，npx 会帮你执行依赖包里的二进制文件。 -- 司徒正美

司徒大大文章写的太简洁了，不过他还是举了例子，我进行了一下精简，如下
``` js
./node_modules/.bin/webpack -v // => npx webpack -v
```
简单来说就是找包执行命令的时候不再关注他在哪了，直接就可以用了。

阮一峰老师的文章更像是官网的翻译加理解

> npx 的原理很简单，就是运行的时候，会到node_modules/.bin路径和环境变量$PATH里面，检查命令是否存在。
> 由于 npx 会检查环境变量$PATH，所以系统命令也可以调用。

另外阮一峰老师还介绍了一个临时安装命令使用的场景，我理解为对上面英语介绍倒数第二句的理解

### 避免全局安装模块
除了调用项目内部模块，npx 还能避免全局安装的模块。比如，create-react-app这个模块是全局安装，npx 可以运行它，而且不进行全局安装。

``` bash
$ npx create-react-app my-react-app

```
上面代码运行时，npx 将create-react-app下载到一个临时目录，使用以后再删除。所以，以后再次执行上面的命令，会重新下载create-react-app。

下载全局模块时，npx 允许指定版本。

``` bash
$ npx uglify-js@3.1.0 main.js -o ./dist/main.js

```
上面代码指定使用 3.1.0 版本的uglify-js压缩脚本。

注意，只要 npx 后面的模块无法在本地发现，就会下载同名模块。比如，本地没有安装http-server模块，下面的命令会自动下载该模块，在当前目录启动一个 Web 服务。

``` bash
$ npx http-server
```

然后阮老师还对官网最后一句话做了解释
### --no-install 参数和--ignore-existing 参数
如果想让 npx 强制使用本地模块，不下载远程模块，可以使用--no-install参数。如果本地不存在该模块，就会报错。

``` bash
$ npx --no-install http-server

```
反过来，如果忽略本地的同名模块，强制安装使用远程模块，可以使用--ignore-existing参数。比如，本地已经全局安装了create-react-app，但还是想使用远程模块，就用这个参数。

``` bash
$ npx --ignore-existing create-react-app my-react-app

```
然后对于官网上的 example 阮老师也挑了重点的做了介绍，如选择指定的 node 版本
``` bash
$ npx node@0.12.8 -v
v0.12.8
```
上面命令会使用 0.12.8 版本的 Node 执行脚本。原理是从 npm 下载这个版本的 node，使用后再删掉。
某些场景下，这个方法用来切换 Node 版本，要比 nvm 那样的版本管理器方便一些。

## 更多参数
### -p 参数
-p参数用于指定 npx 所要安装的模块，所以上一节的命令可以写成下面这样。
``` bash
$ npx -p node@0.12.8 node -v 
v0.12.8
```
上面命令先指定安装node@0.12.8，然后再执行node -v命令。
-p参数对于需要安装多个模块的场景很有用。
``` bash
$ npx -p lolcatjs -p cowsay [command]
```
### -c 参数
如果 npx 安装多个模块，默认情况下，所执行的命令之中，只有第一个可执行项会使用 npx 安装的模块，后面的可执行项还是会交给 Shell 解释。

``` bash
$ npx -p lolcatjs -p cowsay 'cowsay hello | lolcatjs'
# 报错
```
上面代码中，cowsay hello | lolcatjs 执行时会报错，原因是第一项 cowsay 由 npx 解释，而第二项命令localcatjs由 Shell 解释，但是lolcatjs并没有全局安装，所以报错。

-c参数可以将所有命令都用 npx 解释。有了它，下面代码就可以正常执行了
``` bash
$ npx -p lolcatjs -p cowsay -c 'cowsay hello | lolcatjs'

```
-c参数的另一个作用，是将环境变量带入所要执行的命令。举例来说，npm 提供当前项目的一些环境变量，可以用下面的命令查看。

``` bash
$ npm run env | grep npm_

```
-c参数可以把这些 npm 的环境变量带入 npx 命令。

``` bash
$ npx -c 'echo "$npm_package_name"'

```
上面代码会输出当前项目的项目名。

## 执行 GitHub 源码
npx 还可以执行 GitHub 上面的模块源码。
``` bash
# 执行 Gist 代码
$ npx https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d32

# 执行仓库代码
$ npx github:piuccio/cowsay hello
```
注意，远程代码必须是一个模块，即必须包含package.json和入口脚本。

# 最后
因为还没有实际使用过的经验，所以更多的内容从其他大佬哪里白嫖来的知识，做个笔记以观后效。