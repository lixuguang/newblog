---
title: 使用阿里镜像加速brew（转载）
date: 2020-03-03 22:00:00
categories: 
	- 软件使用
tags:
	- HomeBrew
	- Mac
---
> 作者：李旭光
> 引用请标明出处

# 使用 Alibaba 的 Homebrew 镜像源进行加速
平时我们执行 brew 命令安装软件的时候，跟以下 3 个仓库地址有关：
- brew.git
- homebrew-core.git
- homebrew-bottles
通过以下操作将这 3 个仓库地址全部替换为 Alibaba 提供的地址

## 1. 替换 / 还原 brew.git 仓库地址
``` bash
# 替换成阿里巴巴的 brew.git 仓库地址:
cd "$(brew --repo)"
git remote set-url origin https://mirrors.aliyun.com/homebrew/brew.git

#=======================================================

# 还原为官方提供的 brew.git 仓库地址
cd "$(brew --repo)"
git remote set-url origin https://github.com/Homebrew/brew.git
```

## 2. 替换 / 还原 homebrew-core.git 仓库地址
``` bash
# 替换成阿里巴巴的 homebrew-core.git 仓库地址:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git

#=======================================================

# 还原为官方提供的 homebrew-core.git 仓库地址
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://github.com/Homebrew/homebrew-core.git
```

## 3. 替换 / 还原 homebrew-bottles 访问地址
这个步骤跟你的 macOS 系统使用的 shell 版本有关系

所以，先来查看当前使用的 shell 版本

``` bash
echo $SHELL

# 如果你的输出结果是 /bin/zsh，参考下方的 zsh 终端操作方式
# 如果你的输出结果是 /bin/bash，参考下方的 bash 终端操作方式
```
### 3.1 zsh 终端操作方式
``` bash
# 替换成阿里巴巴的 homebrew-bottles 访问地址:
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc

#=======================================================

# 还原为官方提供的 homebrew-bottles 访问地址
vi ~/.zshrc
# 然后，删除 HOMEBREW_BOTTLE_DOMAIN 这一行配置
source ~/.zshrc
```
### 3.2 bash 终端操作方式
``` bash
# 替换 homebrew-bottles 访问 URL:
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile

#=======================================================

# 还原为官方提供的 homebrew-bottles 访问地址
vi ~/.bash_profile
# 然后，删除 HOMEBREW_BOTTLE_DOMAIN 这一行配置
source ~/.bash_profile
```

转载自：http://www.xiegangd.com/article/154055689187484