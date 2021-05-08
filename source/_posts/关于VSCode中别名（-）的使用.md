---
title: VSCode中既想使用path-intellisense插件，又想使用Webpack别名（@）的方法
categories:
  - 前端框架
  - Vue
tags:
  - VSCode插件
  - path-intellisense
  - 路径别名
date: 2021-05-08 08:03:48
---

> 作者：李旭光
> 引用请标明出处

## 前言


<!-- more -->
### 场景：
前端开发我们通常使用VSCode，使用时常见有一个常见的场景：
> 引入一个文件、图片等

如果不利用VSCode的插件，我们通常会写相对路径或者绝对路径，但为了方便写代码，我们通常会利用工具来帮助我们减轻负担，这种情况就引入了今天的主角，VSCode下的插件**path-intellisense**，是的，有了它编辑器就会聪明的自动帮我们提示出文件在什么地方，这的确方便了我们。

但是，在Vue开发过程中，或者说是利用Webpack作为构建工具时我们通常会定义一个变量如：`@` 来简化路径的书写，使代码变得简洁美观，这里的`@`，我们称之为 **路径别名** ，这也是Vue官方十分推荐使用的，但因为路径别名的使用，原来的智能提示路径的插件就会无法正常工作，因为工作中确实遇到了这样的坑，所以简单的写个文记录一下。

### 问题一：因为使用了路径别名（@）导致path-intellisense不能自动提示
这个问题其实比较容易理解，因为路径别名是Webpack构建时使用的，并不是VSCode原生支持的功能，所以它并不认识`@`，这是个什么，所以解决思路就是告诉VSCode `@` 是个什么就好了，专业一点说的话就是加上映射关系

#### 解决步骤
1. 打开path-intellisense插件的setting
2. 找到Mapping配置项
3. 加以下代码
``` json
"path-intellisense.mappings": {
      "@": "${workspaceRoot}/src"
   }
```
4. 搞定！

### 问题二：我们希望 Ctrl + 鼠标左键点击一个外部方法时，能够快速跳转到对应的外部文件。
第二个问题提出时，可能会有人有疑问，上面不是配置完映射关系了嘛，为什么还有下面的问题呢，其实你要是不问，我也有这个问题，上一步操作实际上是告诉了path-intellisense插件该怎么解析`@`这个东西，但是VSCode并不知道啊，但是Ctrl + 鼠标左键的动作又是VSCode负责的事，所以我们也要告诉一下编辑器该如何应对上面的情况，说到这里大家应该就理解了

#### 解决步骤
1. 在项目package.json所在同级目录下创建文件jsconfig.json（这是VSCode的一个可选配置）
2. 在jsconfig.json增加如下配置
``` json
{
    "compilerOptions": {
        "target": "ES6",
        "module": "commonjs",
        "allowSyntheticDefaultImports": true,
        "baseUrl": "./",
        "paths": {
          "@/*": ["src/*"] // 这里是关键代码
        }
    },
    "exclude": [
        "node_modules"
    ]
}
```
3. 搞定

通过上面的解决方案，我们就可以愉快的使用VSCode进行前端开发了，你学废了吗？
