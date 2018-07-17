---
title: 10分钟利用github + hexo搭建个人blog
date: 2018-07-10 13:48:10
tags:
categories: 
- 小玩意
---
# 10分钟利用github + hexo搭建个人blog

本文主要记录使用hexo来搭建基于github的个人blog

## 0 准备工作

* github账号 (github.com/zhaipeihan)

* 简单了解node.js npm

* 好奇的心

## 1 创建仓库

在github上创建一个username.github.io的仓库（username为你自己的用户名）

## 2 hexo初始化

* 安装hexo

```bash
npm install -g hexo-cli
```

* 初始化项目文件夹

```bash
npm init
```

## 3 初步预览

页面生成

```bash
hexo g
```

本地预览（默认 localhost:4000/ ）

```bash
hexo s
```

## 4 部署到github上

项目的根目录下有_config.yml，这是hexo的主要配置文件，我们首先要将我们的github账户使用ssh方式配置上去，我的配置如下

```plain
deploy:
  type: git
  repo: git@github.com:zhaipeihan/zhaipeihan.github.io.git
  branch: master
```

先清理下，防止各种问题

```bash
hexo clean
```

生成并上传

```bash
hexo d -g
```

此处上传至github时可能报"ERROR Deployer not found: git"错误 解决方案如下：

```bash
npm install hexo-deployer-git --save
```

等待生成页面上传之后就可以访问username.github.io来看到自己的博客了

## 5 使用主题

主题爱好因人而异，我这里选择的是Archer（ github.com/fi3ework/hexo-theme-archer ）

接下来的配置过程都是基于Archer主题。主题安装方法见github说明。

## 6 其他配置

接下来说明如何使用hexo的提供的分类和标签功能。

### 6.1 分类

新建一个页面

```bash
$hexo new page categories
```

根据生成的路径找到上一步生成的文件，修改其中的内容，内容如下：

```plain
---
title: categories
date: 2018-07-12 13:28:40
type: "categories"
---
```

以后在写文章的时候在文章顶部增加（注意格式）：

```plain
categories:
- 小玩意
```