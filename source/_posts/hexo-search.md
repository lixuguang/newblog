---
title: 给我的Hexo博客添加文章内容搜索功能
date: 2020-01-13 14:50:21
categories: 
	- 博客技巧
tags: 
	- Hexo
---
> 作者：李旭光
> 引用请标明出处


# 前言
今年我下定决心一定要好好地写博客，完善博客的内容，所以最近把原来记在各种笔记中的文章内容都一一转移到了博客，内容一多想找一篇文章时就变得很麻烦，我得去归档中或者分类中一篇篇的找，所以我觉得是时候该给我的博客添加搜索的功能了，我看了博客的config文件，其中并没有搜索相关的配置，我又看了主题的配置文件，这下让我找到了，有个local_search的选项，于是我就开始了博客搜索的研究。
<!-- more -->
## 主题里的搜索配置
这段代码是这样的，实际上我只需要把 `enable` 从 `false` 变成 `true` 就好了
``` yml

# Local Search
# Dependencies: https://github.com/theme-next/hexo-generator-searchdb
local_search:
  enable: true
  # If auto, trigger search by changing input.
  # If manual, trigger search by pressing enter key or search button.
  trigger: auto
  # Show top n results per article, show all results by setting to -1
  top_n_per_article: 1
  # Unescape html strings to the readable one.
  unescape: false
  # Preload the search data when the page loads.
  preload: false
```
然后我又看了一下上面提供的依赖地址，这里还需要做两步，一个是安装搜索的依赖
``` js
npm install hexo-generator-searchdb
```
接着就是在博客系统的配置最下方加入下面这段话
``` yml
search:
  path: search.xml
  field: post
  content: true
  format: html
```
到这里如果没什么问题，那么搜索功能就加上了，怎么样简单吧。如果你遇到什么问题，可以到上面的地址看一下，上面有详细的说明，我这里就不贴代码了。

# 最后
希望大家都能丰富自己的技术博客，拥有属于自己的一片技术天地。