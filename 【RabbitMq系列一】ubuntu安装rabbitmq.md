---
title: 【RabbitMQ系列一】ubuntu安装rabbitmq
date: 2018-07-19 22:05:23
tags:
categories: 
- 中间件
---
# 【RabbitMQ系列一】ubuntu安装rabbitmq

## 0 背景
之前对于消息队列了解相对较少，这次想从头系统的学习下关于消息队列的相关内容，并记录下学习过程。

## 1 安装
我使用的机器系统为ubuntu 18.04，本文的安装都是基于这个版本。

+ 安装erlang
由于rabbitmq的服务端是用erlang开发的，所以首先要安装erlang的运行环境。

```bash
sudo apt-get install erlang-nox
```

+ 安装Rabbitmq

```bash
sudo apt-get update
sudo apt-get install rabbitmq-server
```

+ 启动、停止、重启rabbitmq

```bash
sudo rabbitmq-server start
sudo rabbitmq-server stop
sudo rabbitmq-server restart
```

## 2 可能会遇到的问题
我在安装过程中遇到了两个问题，这里记录下问题以及解决方案。

1. 安装完成后不能启动

终端报错信息为：“node with name "rabbit" already running on xxxx”

rabbitmq安装完成后自动启动某些进程，导致使用rabbitmq-server 无法启动

解决方案：

查看rabbit相关的进程：

```bash
ps aux | grep 'rabbit'
```

kill掉与rabbit相关的进程：

```bash
kill -9 [pid]
```

2. 启动rabbitmq之后无法访问localhost:15672

从rabbitmq的启动日志可以看到rabbitmq在启动时加载了几个插件，如果是加载了0个插件，则无法访问rabbitmq的管理控制台，因为没有安装相关的Web插件。

解决方案：安装Web插件。

```bash
sudo rabbitmq-plugins enable rabbitmq_management
```

之后打开浏览器输入 http://localhost:15672 
默认用户名密码：guest/guest，就可以看到管理界面了。

![](http://owgxabs76.bkt.clouddn.com/%E9%80%89%E5%8C%BA_003.png)