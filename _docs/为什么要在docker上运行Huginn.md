---
title: 为什么要在 docker 上运行 Huginn
layout: docs
permalink: /docs/为什么要在docker上运行Huginn.html
prevPage: network-image-node.html
nextPage: map-node.html
---

你可以通过 [docker](http://www.docker.com/) 部署 Huginn。

一旦你已经安装好 docker，那么使用 docker 运行 huginn 是快速容易的。 docker 容器非常适合进行测试或正式部署。 Huginn 使用环境变量进行安装配置, 而不只是用 .env 文件，Docker 容器可以把需要的环境变量使用命令自动部署完毕。

## 启动容器

### 快速启动 Huginn

#### OSX 下使用 Kitematic 安装

1. 下载以及安装 [Kitematic](https://www.docker.com/docker-kitematic)
2. 启动 Kitematic 然后搜索 `cantino/huginn`
3. 点击 `create` 然后等待容器下载完毕和启动
4. 点击图标边上的链接 'WEB PREVIEW'
5. 使用默认的帐号密码登录你的 Huginn 网站

#### OSX/Windows/Linux 平台下使用 docker machine 安装

1. 根据你的系统下载 [docker machine](https://docs.docker.com/machine/#installation) 
2. 按照安装向导操作直到你可以成功运行 `docker ps`
3. 使用这个命令获取正在运行的 VM 虚拟机的 ip `docker-machine ls`
4. 使用这个命令启动你的 Huginn 容器 `docker run -it -p 3000:3000 cantino/huginn`
5. 在浏览器里面打开 Huginn [http://docker-machine ip:3000](http://<docker-machine ip>:3000)
6. 使用默认帐号密码登录 Huginn 网站

#### Linux

1. 根据这个步骤安装 docker [install instructions](https://docs.docker.com/installation/)
2. 使用这个命令启动你的 Huginn 容器 `docker run -it -p 3000:3000 cantino/huginn`
3. 在浏览器里面打开 Huginn [http://localhost:3000](http://localhost:3000)
4. 使用默认帐号密码登录 Huginn 网站

## 配置和连接到数据库容器

根据这个链接上面的介绍 [instructions on the docker hub registry](https://registry.hub.docker.com/u/cantino/huginn/) 利用环境变量配置 Huginn 并连接到外部的 MySQL 或 PostgreSQL 数据库容器.

## 运行多个 Huginn 进程在独立的容器里面

使用这个镜像`cantino/huginn-single-process`，你可以轻松地在一个独立的容器内运行 Huginn 的每一个需要的进程，并且可以在需要的时候扩充 (scale) 它。 你可以看看这篇文章 [Docker hub](https://hub.docker.com/r/cantino/huginn-single-process/) 还有这篇 [documentation for the container](https://github.com/cantino/huginn/tree/master/docker/single-process)

### 其他的设置:

其他的 Docker 设置:

* 如果你不想使用官方的 repo, 也可以看看这个: https://registry.hub.docker.com/u/andrewcurioso/huginn/
* 如果你想在单独的容器内运行 Huginn 的 web 进程和 job worker 进程, 这里也有别的方法 https://github.com/hackedu/huginn-docker。

> 本文由 [Huginn 中文网](http://huginn.cn) 翻译，已经获得项目作者授权，项目原文访问 [Why run Huginn with docker](https://github.com/cantino/huginn/blob/master/doc/docker/install.md)

