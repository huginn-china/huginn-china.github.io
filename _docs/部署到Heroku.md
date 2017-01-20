---
title: 部署到 Heroku
layout: docs
permalink: /docs/部署到Heroku.html
prevPage: network-image-node.html
nextPage: map-node.html
---

如果要将 Huginn 部署到 Heroku 平台，我们**推荐你使用最便宜的付费方案**，但是，如果你要使用免费方案的话，你需要注意以下几点：

* 使用 Heroku 免费方案的用户，其每个网站在 30 分钟内无人访问后便会自动关闭，再有人访问时才会自动重新打开，因此为了使 Huginn 网站能长时间运行，可以使用类似 [uptimerobot](https://uptimerobot.com/) 的网站监控服务，不停地ping你的 Huginn 网站地址；
* Heroku 免费方案只允许用户所有 app 每个月运行总时长不超过 550 个小时（绑定信用卡的用户可额外再获得 450 个小时），这意味着你无法保证部署好的 Huginn 网站每个月每天都在运行，因此，你可选择绑定信用卡，获得额外时长，从而使得Huginn 网站能每天都在运行；
* Heroku 免费方案提供给用户的 Postgres 数据库存储的数据不能超过 10000 行，因此你需要控制 Agent 生成事件的保留周期，同时限制数据库中 Agent 日志文件的长度，比如 `heroku config:set AGENT_LOG_LENGTH=20`。

### 初次部署到Heroku的操作步骤：

1.  注册 [Heroku](https://www.heroku.com/) 平台账号，然后下载安装 [Heroku Toolbelt](https://toolbelt.heroku.com/)；
2.  远程登录 Heroku： `heroku login`
3.  创建 app：`heroku create xxx`（xxx 为 app 的名字，下同）；
4.  将 app 下载到本地：`heroku git:clone --app xxx`
5.  将 [huginn](https://github.com/cantino/huginn) 源代码全部下载拷贝到本地 app 的文件夹内；
6.  进入 app 文件目录，依次执行命令：`cp .env.example .env`  和 `bundle`；
7.  提交代码变更：`git add .`、`git commit -am 'commit code'`；
8.  最后，执行脚本：`bin/setup_heroku`，完成部署。

> Windows 用户可能无法使用 `bundle` 命令，你需要安装虚拟机操作，或者可以使用云 IDE 来完成以上步骤，比如[Cloud9](https://c9.io/)。

### 更新已经部署好的 Huginn

Huginn 的功能还在继续开发过程中，在将 Huginn 部署到 Heroku 平台之后，可以通过以下命令来更新 Huginn 的代码：

```
git fetch origin
git merge origin/master
git push -f heroku master # 更新 Heroku 平台上的 Huginn 代码
heroku run rake db:migrate # 迁移数据库到最新状态的 Huginn（尽管不需要每次更新时都运行该命令，但是安全起见，最好每次更新代码时，都运行该命令）
```

### 使用自己的邮箱服务器

在安装过程中默认使用的是 SendGrid 的邮箱服务器（安装后需要自行配置），你也可以使用其他邮箱服务器，下面以谷歌邮箱服务器为例，需要进行以下配置：

```
heroku config:set SMTP_DOMAIN=google.com
heroku config:set SMTP_USER_NAME=you@gmail.com
heroku config:set SMTP_PASSWORD=somepassword
heroku config:set SMTP_SERVER=smtp.gmail.com
heroku config:set EMAIL_FROM_ADDRESS=you@gmail.com # 指定显示的发件人邮箱地址
```
> 假如你的 SMTP（邮件传输协议）密码中包含特殊字符（比如 #、&、$ 等），你需要将密码包含在引号当中，像这样：SMTP 密码 = “My$Password#With&SpecialChars”，假如你没有这么做的话，你会在 logs 文件中看到类似如下的错误信息：
Exception during check. end of file reached: /usr/lib/ruby/2.2.0/net/protocol.rb:153:in read_nonblock' /usr/lib/ruby/2.2.0/net/protocol.rb:153:inrbuf_fill' /usr/lib/ruby/2.2.0/net/protocol.rb:13...

### 备份你的数据

参见 [Heroku 的官方文档](https://devcenter.heroku.com/articles/heroku-postgres-import-export)

### 同时运行多个 Job Worker

在默认情况下，Huginn 每次只能运行一个 Agent，使用很多的 Agent 或者频繁地调用外部服务可能需要更多的 DelayedJob worker（见 Job Management 页面）。为了同时运行多个 Agent，可以同时运行多个 Job Worker，需要重新配置 Procfile 文件，具体设置请参见：https://github.com/cantino/huginn/blob/master/Procfile#L33-L50

> 本文由 [Huginn 中文网](http://huginn.cn) 翻译并修改，已经获得项目作者授权，项目原文访问 [Deploy to Heroku](https://github.com/cantino/huginn/blob/master/doc/heroku/install.md)

