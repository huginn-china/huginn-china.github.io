---
title: 使用 PostgreSQL 代替 Mysql
layout: docs
permalink: /docs/使用PostgreSQL代替Mysql.html
prevPage: 为什么要在docker上运行Huginn.html
nextPage: 在Vagrant和VirtualBox上面部署Huginn.html
---
# 使用 PostgreSQL 代替 Mysql

这是 [Novice setup guide][novice-setup-guide] 的附录，你可以通过本篇向导使用 PostgreSQL 代替 Mysql 。

### 安装 PostgreSQL

以下命令能使你更快的运行，但是在 production 环境你不应该在 `postgres` 用户下运行应用。显而易见的这个用户可以在应用上创建脚本。

    sudo apt-get install postgresql-9.1 postgresql-server-dev-9.1
    # allow password login
    sudo sh -c 'echo "local all postgres md5" >> /etc/postgresql/9.1/main/pg_hba.conf'
    # by default postgres user doesn't have one.
    sudo -u postgres psql -U postgres -d postgres -c "alter user postgres with password 'yourpassword';"

### 修改你的 Mysql 依赖为 PostgreSQL

选项 1:

在 `Gemfile` 文件中，注释 `mysql2` 并添加 `pg`  gem。

    #gem 'mysql2'
    gem 'pg'

选项 2:

在你的环境中一直运行 `ON_HEROKU=true`，这将促使你的 Gemfile 使用 `pg` gem。例如，一旦你按照下面的说明修改好 `.env` 文件，就可以执行操作：`ON_HEROKU=true` `bundle` 和 `ON_HEROKU=true` `foreman start`。

### 编辑 Postgres 数据库连接配置

在 `.env` 文件中，编辑数据库连接配置如下：

    DATABASE_ADAPTER=postgresql
    DATABASE_ENCODING=utf8
    DATABASE_RECONNECT=true
    DATABASE_NAME=huginn_development
    DATABASE_POOL=5
    DATABASE_USERNAME=postgres
    DATABASE_PASSWORD="yourpassword" 
    DATABASE_HOST="127.0.0.1"
    DATABASE_PORT=5432

### 继续安装

之后像平常一样安装就可以了。

[novice-setup-guide]: https://github.com/cantino/huginn/wiki/Novice-setup-guide
[postgresql]: http://www.postgresql.org/

> 本文由 [Huginn 中文网](http://huginn.cn) 翻译，已经获得项目作者授权，项目原文访问 [Using PostgreSQL instead of MySQL](https://github.com/cantino/huginn/wiki/Using-PostgreSQL-instead-of-MySQL)

