---
title: 在 Ubuntu/Debian 上安装 Huginn
layout: docs
permalink: /docs/在Ubuntu-Debian上安装Huginn.html
prevPage: 本地运行Huginn.html
nextPage: Huginn的Apache配置.html
---

## 注意事项

由于文章中包含了很多你可能用到的例子和命令，所以这这份指南会很长。

这份安装指南是基于 Ubuntu/Debian 创建的，已经在上述的系统上完成测试。请阅读[必需的硬件和操作系统](https://github.com/cantino/huginn/blob/master/doc/manual/requirements.md)来了解所需的软硬件要求。

这是官方用来指导部署生产环境下 Huginn 的指南。如果需要在开发环境中进行安装或者想了解其他安装选项，请查看[开始](https://github.com/cantino/huginn#getting-started)。

以下步骤已被证明是有效的，在使用本指南时请务必谨慎以免有所偏离，并确保你没有违反任何我们所要求的安装环境。举个例子，很多人运行会出现权限问题，是因为他们改变了文件夹的位置，或者用其他用户运行了服务。

如果你在这份指导上发现任何 bug 或者错误，请提交一个 pull request。

如果没有特别声明，所有的命令都将运行在超级用户权限下。

当你在安装中遇到任何问题的时候，请查看[问题解决](https://github.com/cantino/huginn/blob/master/doc/manual/installation.md#troubleshooting)一章节。

## 概述

Huginn的安装需要以下几个部分

1. 软件包/独立包
2. Ruby
3. 系统用户
4. 数据库
5. Huginn
6. Nginx

## 1. 软件包/独立包

Debian 没有默认安装 sudo，要确保你的系统是最新的然后安装。

	 # run as root!
	 apt-get update -y
	 apt-get upgrade -y
	 apt-get install sudo -y

**注释：** 在整个安装过程中，一些文件需要人工修改，如果你对 vim 很熟悉的话，可以将 vim 设置为命令行下默认的编辑器。如果你不熟悉 vim 的话，请跳过这一步，并继续使用默认的编辑器。

	 # Install vim and set as default editor
    sudo apt-get install -y vim
    sudo update-alternatives --set editor /usr/bin/vim.basic

引入 node.js 仓库（Ubuntu 和 Debian Jessie 可以跳过这一步）

    curl -sL https://deb.nodesource.com/setup_0.12 | sudo bash -

安装需要的包（需要先编译 Ruby 和本地 Ruby gems 的扩展）

    sudo apt-get install -y runit build-essential git zlib1g-dev libyaml-dev libssl-dev libgdbm-dev libreadline-dev libncurses5-dev libffi-dev curl openssh-server checkinstall libxml2-dev libxslt-dev libcurl4-openssl-dev libicu-dev logrotate python-docutils pkg-config cmake nodejs graphviz

## 2. Ruby

在 Huginn 的生产版本中，如果使用了像 [RVM](http://rvm.io/), [rbenv](https://github.com/sstephenson/rbenv) 或者 [chruby](https://github.com/postmodern/chruby) 这种 Ruby 的版本管理工具，会经常导致难以诊断的问题。所以我们不建议用版本管理程序来安装 Ruby，我们强烈建议各位根据我们下面的步骤使用系统自带的 Ruby。

如果有老版本的 Ruby 版本，先移除：

    sudo apt-get remove -y ruby1.8 ruby1.9

下载新的 Ruby 版本并且进行编译：

    mkdir /tmp/ruby && cd /tmp/ruby
    curl -L --progress http://cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.0.tar.bz2 | tar xj
    cd ruby-2.3.0
    ./configure --disable-install-rdoc
    make -j`nproc`
    sudo make install

用 gem 安装 bundler 和 foreman：

    sudo gem install rake bundler foreman --no-ri --no-rdoc

## 4. 数据库

安装数据库的包

    sudo apt-get install -y mysql-server mysql-client libmysqlclient-dev

    # Pick a MySQL root password (can be anything), type it and press enter,
    # retype the MySQL root password and press enter

检查安装的 MySQL 版本（只有当版本号大于 5.5.3 才能让`.env`配置文件正常运行）

    mysql --version

保证安装安全

    sudo mysql_secure_installation

登陆 MySQL

    mysql -u root -p

    # password输入MySQL的root密码

创建一个 Huginn 的用户，不要先键入 `mysql>`，这部分是要先做的。之后，选择一个真正的密码，并在命令行中替换 `$password`

    mysql> CREATE USER 'huginn'@'localhost' IDENTIFIED BY '$password';

确保你可以使用 InnoDB 引擎，它可以保证一个长索引。

    mysql> SET storage_engine=INNODB;

    # 如果这一步失败，检查你MySQL的config文件 (e.g. `/etc/mysql/*.cnf`, `/etc/mysql/conf.d/*`)
    # 查看innodb的设置 "innodb = off"

保证 Huginn 用户可以允许访问数据库

    mysql> GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, LOCK TABLES ON `huginn_production`.* TO 'huginn'@'localhost';

退出数据库

    mysql> \q

尝试用新用户连接新的数据库

    sudo -u huginn -H mysql -u huginn -p -D huginn_production

    # Type the password you replaced $password with earlier
    # 输入你之前选择的真实密码来代替 $password

你现在应该看到 `ERROR 1049 (42000): Unknown database 'huginn_production'`，这说明我们操作是正确的。因为我们将会一会再创建数据库。

现在完成了数据库部分的安装，你可以继续剩下部分的安装。

## 6. Huginn

### 从源处克隆

    # 我们将安装 Huginn 在我们的 home 文件下的 “huginn” 用户文件夹
    cd /home/huginn

    # 从 Huginn 的仓库克隆
    sudo -u huginn -H git clone https://github.com/cantino/huginn.git -b master huginn

	 # 去 Huginn 的安装目录
    cd /home/huginn/huginn

    # 拷贝 Huginn 的默认配置
    sudo -u huginn -H cp .env.example .env

    # 在目录下创建 log/, tmp/pids/ and tmp/sockets/ directories
    sudo -u huginn mkdir -p log tmp/pids tmp/sockets

    # 确保 Huginn 可以向 log/ 和 tmp/文件夹中写入数据
    sudo chown -R huginn log/ tmp/
    sudo chmod -R u+rwX,go-w log/ tmp/

    # 确保权限设置正确
    sudo chmod -R u+rwX,go-w log/
    sudo chmod -R u+rwX tmp/
    sudo -u huginn -H chmod o-rwx .env

    # 拷贝 Unicorn 的默认配置文件
    sudo -u huginn -H cp config/unicorn.rb.example config/unicorn.rb

### 配置

    # 更新 Huginn 的配置文件，然后按照下面的说明继续做
    sudo -u huginn -H editor .env

如果你使用本地的数据库服务器，数据库的配置文件看起来会是这样（使用你先前设置的 huginn 的 MySQL 用户密码）：

    DATABASE_ADAPTER=mysql2
    DATABASE_RECONNECT=true
    DATABASE_NAME=huginn_production
    DATABASE_POOL=20
    DATABASE_USERNAME=huginn
    DATABASE_PASSWORD='$password'
    #DATABASE_HOST=your-domain-here.com
    #DATABASE_PORT=3306
    #DATABASE_SOCKET=/tmp/mysql.sock

    DATABASE_ENCODING=utf8
    # MySQL only: If you are running a MySQL server >=5.5.3, you should
    # set DATABASE_ENCODING to utf8mb4 instead of utf8 so that the
    # database can hold 4-byte UTF-8 characters like emoji.
    #DATABASE_ENCODING=utf8mb4

**注意：** 注释了 RAILS_ENV 设置以确保实在生产环境中运行的 Huginn

    RAILS_ENV=production

如果有需要的话，改变 Unicorn 的配置文件，在[requirements.md](./requirements.md#unicorn-workers)中有一部分是专门解释并且建议合适的 unicorn 数量。

    
    # 如果你有很高的实例需要运行，那么就增加 workers 的数量
    # 如果你的服务器至少有 2 GB 的内存，那么对大部分用户来说，2 个足够多了
    # 减少 worker 数量到 1
    sudo -u huginn -H editor config/unicorn.rb


**注意:** 保证你修改了 `.env` 和 `unicorn.rb` 来确保程序正常运行

**注意:** 我们建议使用 HTTPS，如果有需要的话，请访问 [使用 HTTPS](https://github.com/cantino/huginn/blob/master/doc/manual/installation.md#using-https) 查看附加的步骤。

**注释:** 为了保证你的修改被保存，每次完成初始安装的时候，都要重新导入开始的脚本文件到 `.env`, `unicorn.rb` 或 `Procfile`!(详情见 [Install Init Script](https://github.com/cantino/huginn/blob/master/doc/manual/installation.md#install-init-script))

### 安装Gems

**注释:** 在bundler 1.5.2 版本，你可以使用 `bundle install -jN` (N 是进程数量)唤起多个进程来进行安装，还可以在一个可测量的差别时间内安装多个gem（快 60%）。用 `nproc` 检查你的处理器数量。更多信息请看 [post](http://robots.thoughtbot.com/parallel-gem-installing-using-bundler)。首先要确定你的 bundler >= 1.5.2 (运行 `bundle -v`)，有很多 [issues](https://devcenter.heroku.com/changelog-items/411) 已经在1.5.2版本[修复](https://github.com/bundler/bundler/pull/2817)。

    sudo -u huginn -H bundle install --deployment --without development test

### 初始化数据库

	 # 创建数据库
    sudo -u huginn -H bundle exec rake db:create RAILS_ENV=production

    # 迁移到最新的版本
    sudo -u huginn -H bundle exec rake db:migrate RAILS_ENV=production

    # 创建管理员用户和示例 agent，并用默认的 admin/password 登陆
    sudo -u huginn -H bundle exec rake db:seed RAILS_ENV=production SEED_USERNAME=admin SEED_PASSWORD=password

当完成的时候查看 `See the Huginn Wiki for more Agent examples!  https://github.com/cantino/huginn/wiki`

**注释:** 这将会创建初始用户，你可以通过设置环境变量中的 `SEED_USERNAME` 和 `SEED_PASSWORD` 来改变你的用户名和密码。如果你不想改变密码（将会设置为默认密码），请等待全部安装完毕上线到互联网之后，你可以登陆到服务器上来改变你的密码。

### 编辑资源文件

    sudo -u huginn -H bundle exec rake assets:precompile RAILS_ENV=production

### 安装初始脚本

Huginn 使用 [foreman](http://ddollar.github.io/foreman/) ，它会根据 `Procfile` 来迁移初始化脚本

修改 [`Procfile`](https://github.com/cantino/huginn/blob/master/Procfile)，并选择一个我们建议的修改方案来适应生产环境。

    sudo -u huginn -H editor Procfile

注释这[两行](https://github.com/cantino/huginn/blob/master/Procfile#L6-L7)

    web: bundle exec rails server -p ${PORT-3000} -b ${IP-0.0.0.0}
    jobs: bundle exec rails runner bin/threaded.rb

使用这[两行](https://github.com/cantino/huginn/blob/master/Procfile#L24-L25) 或 [这些](https://github.com/cantino/huginn/blob/master/Procfile#L28-L31) (即移除注释)

    # web: bundle exec unicorn -c config/unicorn.rb
    # jobs: bundle exec rails runner bin/threaded.rb

引入初始化脚本：

    sudo bundle exec rake production:export

**注释：** 当你每次修改 `.env` 或者 `Procfile` 的时候，一定要重新导入初始化脚本。

### 建立 Logrotate

    sudo cp deployment/logrotate/huginn /etc/logrotate.d/huginn

### 确保你的Huginn实例正在运行

    sudo bundle exec rake production:status

## 7. Nginx

**注释：** Nginx 是一个官方建议给 Huginn 使用的服务器。如果你不想使用 Nginx 作为你的服务器，你可以移步到 wiki 查看 [apache](https://github.com/cantino/huginn/wiki/Apache-Huginn-configuration) 的使用

### 安装

    sudo apt-get install -y nginx

### 网页设置

拷贝示例的网页设置：

    sudo cp deployment/nginx/huginn /etc/nginx/sites-available/huginn
    sudo ln -s /etc/nginx/sites-available/huginn /etc/nginx/sites-enabled/huginn

确保你修改了配置文件来保证你的建立，如果你运行了多个 nginx 网页，那么要 `listen` 指令中移出 `default_server` 参数。

    # Change YOUR_SERVER_FQDN to the fully-qualified
    # domain name of your host serving Huginn.
    sudo editor /etc/nginx/sites-available/huginn

如果 nginx 只运行了一个 huginn 服务，那么删除默认的 nginx 初始网页：

    sudo rm /etc/nginx/sites-enabled/default

**注释：** 如果你想使用我们建议的 HTTPS，用 `huginn-ssl` 代替 Nginx 配置文件中的 `huginn`。在 [使用 HTTPS ](https://github.com/cantino/huginn/blob/master/doc/manual/installation.md#using-https) 查看更多细节。

### 测试配置文件

使用下面的命令测试你的 Nginx 配置文件是否正确：

    sudo nginx -t
    
你应该看到 `syntax is okay` 和 `test is successful`。如果你看到错误信息，那么检查你 Nginx 配置文件中的`huginn` 或 `huginn-ssl` 的排印错误等，根据错误信息来排查错误。

### 重启

    sudo service nginx restart

# 完成

### 第一次登陆

在浏览器中登陆你的服务器地址，进行第一次 Huginn 的登陆。Huginn 已经建立好了一个默认的账号给你，你可以使用下面的来登陆：

    admin (or your SEED_USERNAME)
    password (or your SEED_PASSWORD)
    
**享受吧！** :sparkles: :star: :fireworks:

你可以使用 `cd /home/huginn/huginn && sudo bundle exec rake production:start` and `cd /home/huginn/huginn && sudo bundle exec rake production:stop`来开始或者停止你的 Huginn 服务。

确保你已经阅读了在完成 Huginn 安装后怎么去[更新](./update.md)它！你还可以用 [Capistrano](./capistrano.md) 来保证你的安装始终是最新版本的。

## 一些进阶的设置

### 使用HTTPS

在 Huginn 上使用 HTTPS。

1. 在 .enb
	1. 设置 `FORCE_SSL` 为 `true`。

2. 使用 `huginn-ssl` 代替 `huginn` 配置：
	1. `sudo cp deployment/nginx/huginn-ssl /etc/nginx/sites-available/huginn`
	2. 更新 `YOUR_SERVER_FQDN`
	3. 更新 `ssl_certificate` 和 `ssl_certificate_key`.
	4. 查看配置文件，考虑应用其他安全和性能提高的特性

重启 Nginx，导入初始化的脚本，然后重新启动 Huginn：

	cd /home/huginn/huginn
	sudo service nginx restart
	sudo bundle exec rake production:export

不鼓励使用自签名的证书，如果使用的话，你必须按照正确顺序的步骤，之后生成证书。

	sudo mkdir -p /etc/nginx/ssl/
	cd /etc/nginx/ssl/
	sudo openssl req -newkey rsa:2048 -x509 -nodes -days 3560 -out huginn.crt -keyout huginn.key
	sudo chmod o-r huginn.key

## 故障排查

如果在安装过程中遇到了问题，请确保你是按照正确的顺序安装，没有错过其中的任何一步。

当你的 Huginn 实例没有运行，运行下面的脚本来进行检查。

	cd /home/huginn/huginn
	sudo bundle exec rake production:check

如果还有其他的问题，我们很遗憾的告诉你需要查看各种日志文件来确定错误信息。

#### Nginx 错误日志 `/var/log/nginx/huginn_error.log`

这个文件应该是空的，但是是第一个你需要检查的地方。因为 `nginx` 是第一个处理发送到 Huginn 请求的应用。

通常的问题：

* `connect() to unix:/home/huginn/huginn/tmp/sockets/unicorn.socket failed`：这个是 Unicorn 应用的服务没有运行，确保你注释了 [Profile](https://github.com/cantino/huginn/blob/master/doc/manual/installation.md#install-init-script) 文件中，位于 `PRODUCTION` 下面的两个示例配置文件中的一个。同时你的 unicorn 的配置文件要存在。
* `138 open() "/home/huginn/huginn/public/..." failed (13: Permission denied)`: nginx 用户（默认是`www-data`）需要能读取 `/home/huginn/huginn/public` 目录

### Unicorn 日志  `/home/huginn/huginn/log/unicorn.log`

这里应该包含了 HTTP 请求的入口，类似于：`10.0.2.2 - - [18/Aug/2015:21:15:12 +0000] "GET / HTTP/1.0" 200 - 0.0110`

如果你发现了 ruby 的回溯信息，或者以下的其他错误信息：

* 在 `/home/huginn/huginn/config/unicorn.rb` 的配置文件不存在
* Gem 的组件没有[安装](https://github.com/cantino/huginn/blob/master/doc/manual/installation.md#install-gems)

### Rails 应用的日志 `/home/huginn/huginn/log/production.log`

这个文件非常的冗长，如果在你使用 Huginn 的时候出现 `We're sorry, but something went wrong.` ，这是一个典型的示例回溯信息，可以帮助你或者 Huginn 的开发者定位问题。

```
NoMethodError (undefined method `name' for nil:NilClass):
  app/controllers/jobs_controller.rb:6:in `index'
  config/initializers/silence_worker_status_logger.rb:5:in `call_with_silence_worker_status'
```

### Runit/后台进程日志 /home/huginn/huginn/log/\*/current

如果你的某一个 agent 没有正确的运行，这些文件会包含错误信息或者其他回溯信息。最简单的调试 agent 的方式就是看你的日志文件，当你在 Huginn 的网页上改变或者触发 agent 的时候，都会产生这些日志文件。

这些日志文件的位置取决于你的 Procfile 文件，这些命令会给你可用的日志文件列表：

`ls -al /home/huginn/huginn/log/*/current`

当你想要观察后台进程的时候，你可以轻松的查看所有改变的文件：

```
tail -f /home/huginn/huginn/log/*/current
```

### 还有问题？😿

你可能发现其他你无法解决的错误信息或者意外的回溯信息。请新建一个新的包含足够的信息的 issue ，这样你可以获得这个问题的解决办法。

> 本文由 [Huginn 中文网](http://huginn.cn) 翻译，已经获得项目作者授权，原文请访问 [Installation from source](https://github.com/cantino/huginn/blob/master/doc/manual/installation.md)


