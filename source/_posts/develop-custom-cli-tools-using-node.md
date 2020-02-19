---
title: 使用node开发自定义cli工具
date: 2020-02-19 23:00:00
categories: 
	- 前端工程化
tags:
	- 脚手架
	- CLI
---
> 作者：李旭光
> 引用请标明出处

# 前言
这篇文章想写一写前端工程化相关的内容，原因一呢是是结合公司业务给自己定的业绩指标包含这些内容，另外一个原因是因为听了网易前端唐磊说过的一句话，前端leader干什么，很重要的工作就是前端工程化，高级以上不懂前端工程化很难。

今天听了公开课讲到了用node写一个自己的cli，说实话正是工作所需，课程讲的有点快，没有从头跟下来，自己上完了课又上网上找了些资料，终于把步骤弄明白了，下面就把如何自定义一个cli来帮助提升开发效率。同时也完成了一个业务指标，心里美滋滋。

# 准备
如果你看到这篇文章，也想跟着我的步骤写一下这个自定义cli，那么如下的知识还是有一些为好。
- 基础的nodejs相关知识

没错就只需要会一些node的基础知识就可以了，接下来正式开始

# 初始化
首先，我们要给我们的命令行工具起个名字，比如我们熟悉的 vue 命令行就是 vue-cli ，因为我写完了要给公司用，所以我起的名字是 tfd-cli ，你们喜欢叫什么你们随意

首先创建一个名字为 `tfd-cli` 的文件目录，然后在目录下执行 `node` 工程的初始化命令
``` js
npm init -y // 初始化项目 -y 默认全部yes的参数
```
命令执行完成后 `tfd-cli` 目录下会生成一个我们熟悉的 `package.json` 文件，我们打开 `package.json` 文件，增加一段代码，如下
``` json
// 追加的代码
"bin": {
	"tfd": "index.js"
}
```
追加完成后，`package.json` 文件中的内容是这样的
``` json
{
  "name": "tfd-cli",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "bin": {
    "tfd": "index.js"
  }
}
```
也就是说当我们执行 `tfd` 命令时它就会找同级目录下的 `index.js` 文件执行其中代码，我们目前还没有 `index.js`，文件，那么我们手动创建一个 `index.js` 的文件，然后在里面写下如下代码
``` js
#!/usr/bin/env node
//使用node开发命令行工具所执行JavaScript脚本必须在顶部加入 #!/usr/bin/env node 声明该命令行脚本是node.js写的

console.log('hello world');

```
执行完这些还不够，因为我们是开发环境所以还有一步操作是要将 `tfd` 命令告知 `npm`，该如何处理，所以我们要执行如下命令
``` js
npm link
```
这里如果执行不成功，请用管理员权限执行，执行完成后我们会得到一个 `package-lock.json` 的文件，内容如下
``` json
{
  "name": "tfd-cli",
  "version": "1.0.0",
  "lockfileVersion": 1
}

```
如此一来，我们就可以在任何目录下执行命令行 `tfd` 就会执行 `index.js` 文件了，这里我们会在控制面板中输出 `hello world` ，怎么样是不是小有成就感，我们接着往下来。

# 创建指令
我们写个命令行工具肯定不是为了输出个 `hello world` 这么简单，而是希望通过用户输入内容后根据条件输出一些东西，那么让我们想想一个命令行工具应该具备哪些指令呢？
- 查看命令行工具版本
- 查看帮助文档
- 初始化模板
- 列出模板类型
- 等等

那么用指令该如何描述呢
``` json
tfd -V|--version //查看工具版本号
tfd -h|--help //查看使用帮助
tfd init <template-name> <project-name> //基于指定模板进行项目初始化
tfd list //列出所有可用模板
```
为了执行命令，这里我们要引入一个 `node` 包叫做 `commander`，因此我们要先执行一下 `install` 命令
``` js
npm install commander
```
接着我们就可以在 `index.js` 里面写指令了。
``` js
// index.js

#!/usr/bin/env node
//使用node开发命令行工具所执行JavaScript脚本必须在顶部加入 #!/usr/bin/env node 声明该命令行脚本是node.js写的
const cmd = require('commander');

// tfd -V|--version
cmd.version('0.1.0');  // -V|--version时输出版本号0.1.0
```
到这一步我们在控制台敲一下 `tfd -V` 你会发现什么也没输出，这是因为到这一步我们还无法解析 `tfd -V` 操作，在这之前我们要知道一个命令
``` js
process.argv // 使用process.argv获取命令行参数
```
当我们把这句话加到 `console.log` 中在 `index.js` 中输出时你会看到控制台打印出
``` js
// index.js

#!/usr/bin/env node
//使用node开发命令行工具所执行JavaScript脚本必须在顶部加入 #!/usr/bin/env node 声明该命令行脚本是node.js写的
const cmd = require('commander');

// tfd -V|--version
cmd.version('0.1.0');  // -V|--version时输出版本号0.1.0
console.log(process.argv)

// 控制台
[ '/usr/local/bin/node', '/usr/local/bin/tfd', '-V' ]
```
接下来我们要让`commander`获取参数执行命令
``` js
// index.js

#!/usr/bin/env node
//使用node开发命令行工具所执行JavaScript脚本必须在顶部加入 #!/usr/bin/env node 声明该命令行脚本是node.js写的
const cmd = require('commander');

// tfd -V|--version
cmd.version('0.1.0');  // -V|--version时输出版本号0.1.0
// console.log(process.argv)
cmd.parse(process.argv);

```
这个时候我们再在控制台输入 `tfd -V` 时，我们就会发现，控制台输出了 `0.1.0`，这样我们就完成了查版本的指令，接下来我们完成其他的指令

``` js
// index.js

#!/usr/bin/env node
//使用node开发命令行工具所执行JavaScript脚本必须在顶部加入 #!/usr/bin/env node 声明该命令行脚本是node.js写的
const cmd = require('commander');

// tfd -V|--version
cmd.version('0.1.0');  // -V|--version时输出版本号0.1.0

// tfd init <template> <project>
cmd
  .command('init <template> <project>') // 参数
  .description('初始化项目模板')
  .action((templateName, projectName) => {
      console.log(templateName, projectName);
  })
 
// tfd list
cmd
  .command('list')
  .description('查看所有可用模板')
  .action(() => {
      console.log(`
          a   a模板
          b   b模板
          c   c模板
      `)
  })
// console.log(process.argv)
cmd.parse(process.argv);

```
这个时候我们在控制台上输入 `tfd -h` 的时候，控制台会输出如下代码
``` js
Usage: tfd [options] [command]

Options:
  -V, --version              output the version number
  -h, --help                 output usage information

Commands:
  init <template> <project>  初始化项目模板
  list                       查看所有可用模板
```
这样我们就实现了自定义命令，我们执行一下 `tfd init template1 project1`，我们可以看到，控制台中输出了 `template1 project1`，也就是说`command`命令后尖括号中指向了`action`中的参数，我们就可以通过判断`action`中的参数做具体的操作了。

通常模板可以选择从本地拷贝一份，但更常用的是从线上拷贝一份，比如从`github`中，接下来我们就看看如何从`github`中拷贝一个模板作为项目的初始化工程

## github上创建模板仓库
首先我们要在github上创建两个仓库 `tpl-1` `tpl-2`，这里为了从`github`中下载仓库我们需要一个`node`包支持，让我们请出`download-git-repo`，别忘了执行安装命令
```
npm install download-git-repo
```
安装完依赖之后让我们再去`index.js`填点东西，首先引入下载依赖，然后是创建下载的 `template` 抽象对象
``` js
#!/usr/bin/env node
//使用node开发命令行工具所执行JavaScript脚本必须在顶部加入 #!/usr/bin/env node 声明该命令行脚本是node.js写的
const cmd = require('commander');
const download = require('download-git-repo');
// 可用模板
const templates = {
    'tpl-1': {
        url: 'https://github.com/lixuguang/tpl-1',
        downloadUrl: 'https://github.com:lixuguang/tpl-1#master',
        description: 'tfd-cli脚手架测试模板1'
    },
    'tpl-2': {
        url: 'https://github.com/lixuguang/tpl-2',
        downloadUrl: 'https://github.com:lixuguang/tpl-2#master',
        description: 'tfd-cli脚手架测试模板2'
    }
}

// tfd -V|--version
cmd.version('0.1.0');  // -V|--version时输出版本号0.1.0

// tfd init <template> <project>
cmd
  .command('init <template> <project>') // 参数
  .description('初始化项目模板')
  .action((templateName, projectName) => {
      // console.log(templateName, projectName);
      let {downloadUrl} = templates[templateName];        
        // 第一个参数是github仓库地址，第二个参数是创建的项目目录名，第三个参数是clone
        download(downloadUrl, projectName, {clone: true}, err => {
            if(err){
                console.log('模板下载失败');
            }else{
                console.log('模板下载成功');
            }
        })
  })
 
// tfd list
cmd
  .command('list')
  .description('查看所有可用模板')
  .action(() => {
    // console.log(`
    //       a   a模板
    //       b   b模板
    //       c   c模板
    //   `)
    // 通过获取templates里的key可以获取到模板名称
    const templateName = Object.keys(templates)
    console.log(templateName)
  })
// console.log(process.argv)
cmd.parse(process.argv);

```
这样当我们执行 `tfd list` 就可以看到有哪些模板了，然后执行`tfd init tpl-1 newproject` 就可以依据 `tpl-1` 模板创建出 `newproject` 工程，这个过程实际上就是从`github`仓库克隆一份`tpl-1`作为模板创建工程`newproject`

***这里需要注意的是download地址跟github仓库地址有点出入，比如github仓库地址是https://github.com/xxx/xxx而下载地址是https://github.com:xxx/xxx***

虽然这样执行完成后就完成了基本的cli的雏形，但是毕竟不灵活，我们在使用`vue-cli`时，它的创建过程是问答式和选择式的，另外每个过程都会有进度显示什么的，那么要怎么添加这些功能呢，我们接着往下做。

## 进阶增加功能
### 使用`inquirer`进行命令行答询
`inquirer` 是一个进行命令行答询的库，通过它我们就可以创建问答式的内容，首先还是安装依赖
```
npm install inquirer
```
### 使用handlebars修改package.json
我们都知道在使用`vue-cli`的初始化命令后，会在项目目录下生成一个`package.json`文件，它就像是这个项目的基因序列一样，影响着项目的整个结构。模板是固定的，那要修改其中的`package.json`符合自己项目的需要，就要用到`handlebars`这个库来改写`package.json`文件，老规矩先安装它
```
npm install handlebars
```
### 使用ora在命令行中显示加载状态
我们在装任何依赖时都会有进度条显示进度，如果没有进度条又没有任何响应，会让用户迷茫，为了友好，我们就要加进度条，这里我们需要引入`ora`这个库来完成进度显示，我们接着安装。
```
npm install ora
```
### 使用chalk和log-symbols增加命令行输出样式
为了让命令行有红红绿绿的效果以及符号效果，我们需要使用`chalk`和`log-symbols`来丰富样式，少废话，接着装
```
npm install chalk log-symbols
```

### 集大成
终于安装完一堆的依赖，别忘了在`index.js`中引入，让我们看看具体如何使用这些库吧。
``` js
// index.js

#!/usr/bin/env node
//使用node开发命令行工具所执行JavaScript脚本必须在顶部加入 #!/usr/bin/env node 声明该命令行脚本是node.js写的
const cmd = require('commander');
const download = require('download-git-repo');

const iq = require('inquirer');       // 命令行答询
const hb = require('handlebars');       // 修改package.json文件
const ora = require('ora');         // 命令行中加载状态标识
const chalk = require('chalk');     // 命令行输出字符颜色
const ls = require('log-symbols');      // 命令行输出符号
const fs = require('fs'); // node fs原生模块

// 可用模板
const templates = {
    'tpl-1': {
        url: 'https://github.com/lixuguang/tpl-1',
        downloadUrl: 'https://github.com:lixuguang/tpl-1#master',
        description: 'tfd-cli脚手架测试模板1'
    },
    'tpl-2': {
        url: 'https://github.com/lixuguang/tpl-2',
        downloadUrl: 'https://github.com:lixuguang/tpl-2#master',
        description: 'tfd-cli脚手架测试模板2'
    }
}

// tfd -V|--version
cmd.version('0.1.0');  // -V|--version时输出版本号0.1.0

// tfd init <template> <project>
cmd
  .command('init <template> <project>') // 参数
  .description('初始化项目模板')
  .action((templateName, projectName) => {
      // console.log(templateName, projectName);
      let {downloadUrl} = templates[templateName];   
      //下载github项目，下载墙loading提示
      const loading = ora('模板下载中...').start();      
      // 第一个参数是github仓库地址，第二个参数是创建的项目目录名，第三个参数是clone
      download(downloadUrl, projectName, {clone: true}, err => {
          if(err){
              // console.log('模板下载失败');
              loading.fail('模板下载失败');
          }else{
              // console.log('模板下载成功');
              spinner.succeed('模板下载成功');
              // 命令行答询
              iq.prompt([
                {
                    type: 'input', // 类型 输入框
                    name: 'name', // 字段 key
                    message: '请输入项目名称', // 描述
                    default: projectName // 默认值
                },
                {
                    type: 'input',
                    name: 'description',
                    message: '请输入项目简介',
                    default: ''
                },
                {
                    type: 'input',
                    name: 'author',
                    message: '请输入作者名称',
                    default: ''
                }
            ]).then(answers => { // answers 是一个对象，对象的 key 为上面答询的 name 的值，value 为 用户输入的值，如果未输入，就取默认值
                // 根据命令行答询结果修改 package.json 文件
                let packageContent = fs.readFileSync(`${projectName}/package.json`, 'utf8'); // 同步方式以 utf-8 字符集获得下载好的项目目录下的 package.json 文件
                let packageResult = hb.compile(packageContent)(answers); // 将用户输入项与原内容混合获得新内容
                fs.writeFileSync(`${projectName}/package.json`, packageResult); // 重新同步方式写入到 package.json 文件中
                // 用chalk和log-symbols改变命令行输出样式
                console.log(ls.success, chalk.green('模板项目文件准备成功！'));
            })
          }
      })
  })
 
// tfd list
cmd
  .command('list')
  .description('查看所有可用模板')
  .action(() => {
    // console.log(`
    //       a   a模板
    //       b   b模板
    //       c   c模板
    //   `)
    // 通过获取templates里的key可以获取到模板名称
    const templateName = Object.keys(templates)
    console.log(templateName)
  })
// console.log(process.argv)
cmd.parse(process.argv);

```

到这里你自己的命令行工具就创建完成了，当然还可以继续丰富，比如加上选择条件等，这个就更复杂了，不在本文中展开，后续会继续改进这个命令行工具，当然如果要将自己的命令行工具给其他人用当然要先发布出去，就像我们要用`vue-cli`，首先要在`npm`上下载下来，同样的我们要想让别人用，就得先上传到`npm`上。

# 发布到npm上
首先你需要有一个npm的账号，没有赶快去注册一个，在控制台中输入`npm login`，它会让你依次输入 `Username` `Password` `Email` ，当你都按照要求输入完之后，成功的话你会获得如下信息`Logged in as XXX on https://registry.npm.org/.`，再接下来执行 `npm publish` 命令，你的自定义脚手架就会发布到`npm`上，供他人下载使用，怎么样，学会了么？

# 后记
这篇文章会有后续持续进化跟进，多篇文章连续，只要我的cli工具还在进化，文章就会继续，欢迎跟进。