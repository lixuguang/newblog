---
title: 基于 GitLab CI/CD 的自动化构建、发布实践
date: 2019-12-31 16:22:22
categories: 
	- 前端工程化
tags: 
	- GitLab
	- CI/CD
	- 自动构建
---
> 作者：李旭光
> 引用请标明出处


# 前言

公司于去年开始代码版本管理从SVN迁移到了Git，采用的GitLab程序做管理，但是自动构建采用的是Jekins，最近在给博客做自动构建的时候了解到的了Github的Actions，我就在想，是不是GitLab也有自己的自动构建能力，因为之前在参与项目的时候也看到GitLab上又CICD相关的内容，但是没有仔细的去看，趁这次机会就一起研究一下。
<!-- more -->

## 说一下目前公司的构建和发布流程
1、手动构建时代，开发人员在测试需要验证环境的时候，在本地执行打包构建命令，然后将包放到服务器上，整个过程30分钟左右。
2、目前公司的构建是要在Jekins中，首先是在Jekins中配置拉去代码的仓库地址和代码分支，写好构建的脚本，在需要构建时候进行构建，一次配置后构建全程只需要点一下构建时间长度跟项目代码需要下载的依赖时间有关，通常不超过5分钟，需要注意的是要在构建前同步一下代码版本
***划重点***
在原来的手动构建时代，代码是以开发本地的代码为准，代码版本很可能跟最新的代码有出入，而且依赖于开发的电脑设备，如果他请假了，那么就GG了；另外通过一次配置后整个构建的时间从30分钟降到了5分钟，一次节省25分钟，那么一个项目周期下节省的工时就非常可观了。

## 为什要使用GitLab CI/CD进行构建
这里实际上没有太大的必要将公司的Jekins替换为GitLab的CI/CD进行自动构建，但是呢，因为公司本身采用的就是GitLab作为代码仓库管理代码，它本身又提供了CI/CD的功能，本着多学一点是一点的原则，我就花点时间研究一下它。

## 什么是 GitLab CI/CD
下面我就要开始把我了解到的GitLab CI/CD的使用方式说一下，从零开始搭建GitLab CI/CD。
### 1. 简要介绍 GitLab CI/CD
代码提交到GitLab上后，满足指定条件之后会触发pipeline进行自动化构建、发布。
***pipeline***可以理解为构建任务，里面剋已包含多个流程，比如下载依赖、运行测试、编译、部署。
那么pipeline什么时候触发，分为几个流程，每个流程做什么，需要在项目的***.gitlab-ci.yml***文件中的定义。
这点呢跟Jekins里面实际上做的也是同样的事，在线下开发做构建时候也是做这些事，只是通过脚本之后这些事都可以交给计算机做了。

### 2. GitLab CI/CD 整体流程
- GitLab CI/CD 的 pipeline 具体流程和操作在 .gitlab-ci.yml 文件中申明。
- 触发 pipeline 后，由 GitLab Runner 根据  .gitlab-ci.yml 文件运行。
- 运行结束后将返回至 GitLab 系统。

#### 2.1 .gitlab-ci.yml 文件
.gitlab-ci.yml 文件是一个申明式文件，用于定义 GitLab CI/CD 流程分为几个阶段，每个阶段分别干什么。

关于具体干什么、怎么干，主要使用命令行和脚本操作，稍后会在实践部分做细致的介绍。

如果涉及一些逻辑的话，会使用脚本（shell）。

#### 2.2 GitLab Runner
GitLab Runner 是 CI 的执行环境，负责执行 gitlab-ci.yml 文件，并将结果返回给 GitLab 系统。Runner 具体可以有多种形式，docker、虚拟机或 shell，在注册 runner 时选定方式。实际上就是运行脚本的容器环境。

## 3. 从零搭建一个 GitLab CI/CD 的基本步骤
上面介绍了一些GitLab构建的主要环节和名词概念，接下来我将给大家介绍一下如何从零搭建一个GitLab CI/CD，一起体验一把GitLab CI/CD的整个流程。
### 3.1 新建一个 GitLab 项目
我这用的是公司的自有仓库，各位可以在开源GitLab上创建自己的项目
### 3.2 配置Runner
GitLab 提供了一些共享的Runner，我们可以不处理Runner，这里可以理解为，它提供了一些现成的脚本运行环境，不需要我们从头配置运行环境，so sweet～
### 3.3 新建 .gitlab-ci.yml 文件
1. 拉取项目到本地
2. 在项目根目录新建 .gitlab-ci.yml 文件
3. 提交 .gitlab-ci.yml 文件
4. 在项目的 CI/CD 中，可以看到 CI/CD 的运行情况
这个过程应该没人不会吧，没技术含量的我们简单一提，实际上最重要的就是.gitlab-ci.yml文件中要怎么去写，示例说明文件如下：
```
// .gitlab-ci.yml 示例说明

image: node
# 定义 stages
stages:
  - build
  - test
# 定义 job
 build 阶段:
  stage: build
  script:
    - echo "build stage"
# 定义 job
发布到测试环境:
  stage: test
  script:
    - echo "test stage"
```

## GitLab CI/CD 实践
在实践部分，这里着重介绍 GitLab Runner 和 .gitlab-ci.yml 文件，主要的流程及遇到的问题和解决方案包含在 .gitlab-ci.yml 文件的介绍过程中。

### 1. GitLab Runner
GitLab Runner 一般由 GitLab 系统维护者管理，配置后，同类项目可以共享，一般不需要进行修改。这里不进行具体介绍，主要介绍下使用过程中的注意点，具体使用可参考 GitLab Runner 文档。（https://docs.gitlab.com.cn/runner/）

#### 1.1 GitLab Runner 使用流程
1. 下载 GitLab Runner
2. 注册 GitLab Runner
3. 使用 GitLab Runner

#### 1.2 GitLab Runner 注意点
在使用 Runner 的过程中，我们遇到了一些问题，下面简要介绍问题及解决方案，不做具体介绍。

##### 1.2.1 配置 Runner 后，push 代码，出发了 pipeline，但一直处于Pending状态

错误信息是：This job is stuck, because you don’t have any active runners that can run this job

注册的 Runner，默认情况下，不会运用没有 `tag` 的 job，可以在 Settings→CI/CD→Runners Settings，去掉 Runner untagged jobs 即可。

##### 1.2.2 GitLab Runner 的类型
有三种类型的 Runner，
- Shared Runners 在整个系统所有项目都可以使用
- Group Runners 注册后，同一个项目下的不同代码库共享
- Specific Runners 需要给项目单独配置，使用 Specific Runners 注意考虑是否需要关闭 Shared Runners、和 Group Runners。

##### 1.2.3 在 GitLab CI 中使用 docker
如果部署使用的是docker方式，那么在部署时需要在 GitLab CI/CD 中使用 docker 打镜像发布。可以参考 Building Docker images with GitLab CI/CD（https://docs.gitlab.com/ee/ci/docker/using_docker_build.html）

##### 1.2.4 在 GitLab CI/CD 中访问 Runner 宿主机目录
我们使用的 Runner executor 是 Dokcer，在 Dokcer volumes 中配置需要访问的目录。

### 2. .gitlab-ci.yml 文件
.gitlab-ci.yml 详细的用法，可参考 GitLab CI/CD Pipeline Configuration Reference 文档（https://docs.gitlab.com/ee/ci/yaml/README.html）

#### 2.1 .gitlab-ci.yml 文件结构介绍
- `image` 是执行 CI/CD 依赖的 Docker 基础镜像。镜像中有 Node、Yarn、Dalp（内部 rsync 工具）。
- `stages` 中定义了我们的 pipeline 分为以下几个过程:
  1. 下载依赖阶段 pre_build
  2. 构建阶段 build
  3. 发布阶段 deploy
- `stage` 申明当前的阶段，在 stages 中使用
- `variables` 用于定义变量
- `before_script` 执行 script 前的操作
- `script` 当前 stage 需要执行的操作
- `changes` 指定 stage 触发条件
- `refs` 指定 stage 触发的分支

下面具体看一下我们这个.gitlab-ci.yml文件实际的样子
```
image: registry.thunisoft.com/gitlab-ci/node:v1.8

variables:
  # $CI_PROJECT_PATH  ：项目id,用于项目唯一区分本项目与其它项目
  # $CI_PROJECT_DIR   ：本地项目路径
  # $PROCESS_PATH     ：临时文件目录(包括日志和一些临时文件)
  NODE_MODULES_PATH: /runner-cache/frontend/$CI_PROJECT_PATH/$CI_BUILD_REF_NAME/node_modules

stages:
  - pre_build # 下载依赖阶段
  - build # 构建阶段
  - deploy # 测试发布阶段

# 下载依赖:
  before_script: # 下载依赖前准备脚本
    # 无 node_modules 文件时，新建 node_modules 文件
    - /bin/bash ./ci/mkdir.sh $NODE_MODULES_PATH
    # 软链 node_modules 到宿主机
    - ln -s $NODE_MODULES_PATH .
    - cd webpack@lixuguang-project

  stage: pre_build
  script:
    - echo "npm install"
    - npm install  --network-timeout 60000 # 安装依赖
  only:
    changes:
      - webpack@lixuguang-project/package.json
    refs:
      - master
      - ci

# 构建:
  stage: build
  variables:
    CI_COMMIT_BEFORE_SHA_PATH: /mnt/gv0/gitlab-runner-cache/$CI_PROJECT_PATH
    CI_COMMIT_BEFORE_SHA_FILE_NAME: $CI_BUILD_REF_NAME.sh
    CI_COMMIT_BEFORE_SHA_FILE: /mnt/gv0/gitlab-runner-cache/$CI_PROJECT_PATH/$CI_BUILD_REF_NAME.sh
  before_script:
    # 建存此次 CI CI_COMMIT_SHA 的文件
    - /bin/bash ./ci/mkfile.sh $CI_COMMIT_BEFORE_SHA_PATH $CI_COMMIT_BEFORE_SHA_FILE_NAME
    # 软链 node_modules 到宿主机
    - ln -s $NODE_MODULES_PATH .
    - rm -rf web/share/*
    - cd webpack@lixuguang-projects
  script:
    # 缓存上次ci
    - source $CI_COMMIT_BEFORE_SHA_FILE
    - echo "CI_COMMIT_BEFORE_SHA=$CI_COMMIT_SHA" > $CI_COMMIT_BEFORE_SHA_FILE
    - python3 ../ci/build.py   # 编译
    - /bin/bash ../ci/commit.sh   # 提交编译结果
  only:
    changes:
      - www_src/**/*
    refs:
      - master
      - ci

# 测试发布:
  stage: deploy
  variables:
    PROCESS_PATH: /mnt/gv0/gitlab-runner-cache/deploy/process/$CI_JOB_ID  # 目录不要换，用于日志服务器获取日志展示
  script:
    - mkdir $PROCESS_PATH # 建立发布临时路径，存放发布配置中间文件和结果日志用
    - dplt $CI_PROJECT_DIR/.deploy_test.yml $CI_PROJECT_PATH $CI_PROJECT_DIR/web/ $PROCESS_PATH
    # dplt 发布yml配置
    - echo "发布完成，错误日志查看http://172.18.78.11:8089/log?path="$PROCESS_PATH
    - echo `ls $PROCESS_PATH/*.log`
  only:
    changes:
      - web/**/*
    refs:
      - test
```

#### 2.2 下载依赖阶段（pre_build stage）
下载依赖的方案是：当 package.json 文件发生变化时，触发 pre_build stage，执行 npm install。下载的 node_modules 放在宿主机下，执行时通过软链获取依赖。

#### 2.3 构建阶段（build stage）
构建阶段，分为 3 部分
1. diff 文件变化
2. 前端 build
3. commit build 后结果

##### 2.3.1 diff 文件变化
每次 CI 时，将当前 CI commit SHA（CI_COMMIT_SHA 变量）存在文件中，存为 CI_COMMIT_BEFORE_SHA 变量， diff 时，git diff 当前 CI 与上次 commit SHA 的变化。

##### 2.3.2 前端 build
根据 git diff 的变化情况，确定本次需要打包的内容。

##### 2.3.3 commit 打包后生成的 HTML 文件
在 GitLab CI/CD 提交代码时，使用 Git 凭证存储，提交打包后的 HTML 文件。Git 凭证存储细节可参考凭证存储文档(https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%87%AD%E8%AF%81%E5%AD%98%E5%82%A8)

#### 2.4 发布阶段（deploy stage）
发布阶段，使用内部的 rsync 工具 dplt 将打包后的 HTML 文件部署。dplt 可配置集群、机器列表。

## 写在最后
以上就是GitLab CI/CD的整个理论到实践的全部过程，实现之后你就可以解放双手了，是不是超爽。

## 参考资料
持续集成是什么？(http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html)

什么是 CI/CD？(https://www.redhat.com/zh/topics/devops/what-is-ci-cd)

GitLab Docs(https://docs.gitlab.com/)

Introduction to CI/CD with GitLab(https://docs.gitlab.com/ee/ci/introduction/)

用 GitLab CI 进行持续集成(https://scarletsky.github.io/2016/07/29/use-gitlab-ci-for-continuous-integration/)

如何实现前端工程的持续集成与持续部署？(https://www.zhihu.com/question/60194439)

基于 GitLab CI 的前端工程CI/CD实践(https://github.com/giscafer/front-end-manual/issues/27)