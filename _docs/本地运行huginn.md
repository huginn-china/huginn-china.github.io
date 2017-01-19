---
title: 本地运行 huginn
layout: docs
permalink: /docs/本地运行huginn.html
prevPage: network-image-node.html
nextPage: map-node.html
---

1. 克隆这个 repo

        $ git clone git@github.com:cantino/huginn.git
        $ cd huginn

2. 安装 [Ruby](http://www.ruby-lang.org/en/downloads/) and [Ruby Gems](https://rubygems.org/pages/download) (如果未安装)

3. 安装 rake 和 bundle (如果未安装):

        $ gem install rake bundle

4. 安装 Huginn 需要的依赖文件

        $ bundle install

5. 安装 [MySQL](http://dev.mysql.com/downloads/)
6. 启动 MySQL 服务:

        $ mysql.server start

6. 复制 *.env.example* 为 *.env*:

        $ cp .env.example .env

8. 生成 `APP_SECRET_TOKEN`:

        $ rake secret

7. 编辑 *.env*文件, 在 `APP_SECRET_TOKEN`里面填入刚才生成的 TOKEN 密匙.
8. 使用实例模板创建正式的 MySQL 数据库:

        $ rake db:create
        $ rake db:migrate
        $ rake db:seed

8. 准备就绪后. 启动本地服务: 

        $ foreman start  

    访问 [http://localhost:3000/](http://localhost:3000/) 然后使用默认的帐号和密码登录即可.


> 本文由 [ Huginn 中文网](http://huginn.cn) 翻译，已经获得项目作者授权，项目原文访问 [install_huginn_locally](https://gist.github.com/mjhea0/b6b58eefc38985380ff9)

