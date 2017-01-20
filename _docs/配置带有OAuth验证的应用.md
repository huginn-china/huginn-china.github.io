---
title: 配置带有 OAuth 验证的应用
layout: docs
permalink: /docs/配置带有OAuth验证的应用.html
prevPage: network-image-node.html
nextPage: map-node.html
---
# 配置带有 OAuth 验证的应用

为了使用新的集成服务，在第一次使用时，你需要通过这些外部应用服务的 OAuth 验证。

## Twitter

如果你还没有一个 Twitter 应用，请先访问 [https://apps.twitter.com](https://apps.twitter.com/)。

你需要给你的应用命名，比如：`John's Huginn` ，再添加一段描述，比如：
`John's personal Huginn system (http://github.com/cantino/huginn)`， Website 中可以填入你的个人网站或者你的 Twitter 账户地址。

Callback URL 这一项非常重要，请参照如下格式填写： `http://<your-huginn-domain.com>/auth/twitter/callback`，其中 `<your-huginn-domain.com>` 换成你的 Huginn 网站的域名。

在创建的应用中，你需要找到并记下你的 "API Key" 和 "API Secret"，然后按照下面的说明操作：

* 如果你的 Huginn 是自己搭建的，打开Huginn中的 `.env` 文件，将 `TWITTER_OAUTH_KEY` 设置为 `API Key`， `TWITTER_OAUTH_SECRET` 设置为 `API Secret`

* 如果你是使用 Docker 容器部署的，将 `HUGINN_TWITTER_OAUTH_KEY` 设置为 `API Key` ，`HUGINN_TWITTER_OAUTH_SECRET` 设置为 `API Secret`。更多环境变量请查看[这里](https://hub.docker.com/r/cantino/huginn/)

* 如果你是在 Heroku 平台上部署的，通过下面的命令设置环境变量：`heroku config:set TWITTER_OAUTH_KEY=YOUR-KEY` 和 `heroku config:set TWITTER_OAUTH_SECRET=YOUR-SECRET`

* 如果你是在 OpenShift 平台上部署的，通过下面的命令设置环境变量：`rhc env set TWITTER_OAUTH_KEY=YOUR-KEY` 和 `rhc env set TWITTER_OAUTH_SECRET=YOUR-SECRET`

在重启你的 Huginn 网站之后，你应该就可以在 Service 页面认证你的 Twitter。

如果你遇到问题，请查看最下方的调试说明。

## 37signals (Basecamp)

如果你还没有一个 37signals 应用，先访问网站 [https://integrate.37signals.com/](https://integrate.37signals.com/) 并创建一个新的应用。

给你的应用选择一个名字，输入你的公司名（或者你自己的名字），然后输入一个网站的网址（可以是任何网址）。

在 "Integration" 部分，你需要勾选 "Basecamp"，而其他的 37signals 服务也是可以勾选的。

在 "Redirect URI" 中，请参照如下格式填写： `https://<your-huginn-domain.com>/auth/37signals/callback`，其中 `<your-huginn-domain.com>` 换成你的 Huginn 网站的域名。

在你创建完应用后，你就会看到 "Client ID" 和 "Client Secret"。跟 Twitter 一样，请按照下面的说明进行操作：

* 如果你的 Huginn 是自己搭建的，打开Huginn中的 `.env` 文件，设置 `THIRTY_SEVEN_SIGNALS_OAUTH_KEY` 设置成 `Client ID`， `THIRTY_SEVEN_SIGNALS_OAUTH_SECRET` 设置为 `Client Secret`

* 如果你是在 Heroku 平台上部署的，通过下面的命令设置环境变量：`heroku config:set THIRTY_SEVEN_SIGNALS_OAUTH_KEY=YOUR-CLIENT-ID` 和 `heroku config:set THIRTY_SEVEN_SIGNALS_OAUTH_SECRET=YOUR-CLIENT-SECRET`

* 如果你是在 OpenShift 平台上部署的，通过下面的命令设置环境变量：`rhc env set THIRTY_SEVEN_SIGNALS_OAUTH_KEY=YOUR-CLIENT-ID` 和 `rhc env set THIRTY_SEVEN_SIGNALS_OAUTH_SECRET=YOUR-CLIENT-SECRET`.

在重启你的 Huginn 网站之后，你应该就可以在 Service 页面认证你的 37signals。

## Wunderlist

如果你还没有一个 Wunderlist 应用，请先访问 [https://developer.wunderlist.com/](https://developer.wunderlist.com/) 并点击 ‘Register Your App’。

给你的应用选择一个名字，还需要填入 URL（可以是你的 huginn 网站或者其他任何网站地址）。

在 "Authorization Callback URL" 中，请参照如下格式填写： `http://<your-huginn-domain.com>/auth/wunderlist/callback`，其中 `<your-huginn-domain.com>` 换成你的 Huginn 网站的域名。

在你创建完应用后，你就会看到 "Client ID" 和 "Client Secret"。也跟 Twitter 一样，请按照下面的说明进行操作：

* 如果你的 Huginn 是自己搭建的，打开 Huginn 中的 `.env` 文件，设置 `WUNDERLIST_OAUTH_KEY` 设置成 `Client ID`， `WUNDERLIST_OAUTH_SECRET` 设置为 `Client Secret`.

* 如果你是在 Heroku 平台上部署的，通过下面的命令设置环境变量：`heroku config:set WUNDERLIST_OAUTH_KEY=YOUR-CLIENT-ID` 和 `heroku config:set WUNDERLIST_OAUTH_SECRET=YOUR-CLIENT-SECRET`。

* 如果你是在 OpenShift 平台上部署的，通过下面的命令设置环境变量：`rhc env set WUNDERLIST_OAUTH_KEY=YOUR-CLIENT-ID` 和 `rhc env set WUNDERLIST_OAUTH_SECRET=YOUR-CLIENT-SECRET`.

在重启你的 Huginn 网站之后，你应该就可以在 Service 页面认证你的 Wunderlist。

## Dropbox

如果你还没有一个 Dropbox 应用，请先访问网址 [https://developer.wunderlist.com/](https://developer.wunderlist.com/) ，再选择 dropbox API ，并允许它可以访问你的所有文件和文件夹，之后点击 ‘Create app’。

给你的应用选择一个名字，还需要填入 URL（可以是你的 huginn 网站或者其他任何网站地址）。

在 "Redirect URIs" 中，请参照如下格式填写： `https://<your-huginn-domain.com>/auth/dropbox/callback`，其中 `<your-huginn-domain.com>` 换成你的 Huginn 网站的域名。

在你创建完应用后，你就会看到 "App key" 和 "App secret"。也跟 Twitter 一样，请按照下面说明进行操作：

* 如果你的 Huginn 是自己搭建的，打开 Huginn 中的 `.env` 文件，设置 `DROPBOX_OAUTH_KEY` 设置成 `App key`，`DROPBOX_OAUTH_SECRET` 设置为 `Client Secret`.

* 如果你是在 Heroku 平台上部署的，通过下面的命令设置环境变量：`rhc env set DROPBOX_OAUTH_KEY=YOUR-APP-KEY` 和 `rhc env set DROPBOX_OAUTH_SECRET=YOUR-APP-SECRET`。

* 如果你是在 OpenShift 平台上部署的，通过下面的命令设置环境变量：`rhc env set WUNDERLIST_OAUTH_KEY=YOUR-APP-KEY` 和 `rhc env set WUNDERLIST_OAUTH_SECRET=YOUR-APP-SECRET`.

在重启你的 Huginn 网站之后，你应该就可以在 Service 页面认证你的 Dropbox。

## Tumblr（待验证修改）

为了整合 Tumblr，你必须在 huginn 的 `.env` 文件中定义 `OAuth key` 和 `secret`。但是，获得 `OAuth key` 并没有那么容易，下面是详细地操作说明。

首先，如果你的 Huginn 是建立在 Apache 或者 Nginx 的网站服务器上，并且使用 HTTP 基本身份验证机制，你必须使用 `htpasswd` 命令来创建一个用户名和密码，从而提供访问的回调 URL：`sudo htpasswd -m /path/to/.htpasswd callback`

登陆到该网址 [(https://www.tumblr.com/oauth/apps)](https://www.tumblr.com/oauth/apps)，点击 “+ Register application” 按钮，输入你的应用名（“Huginn”就是一个有效的应用名），输入一段文字说明，文字说明要尽量清楚地表达你的意图，以防 Tumblr 审核人员单方面地禁止你的 Huginn 使用其服务。默认地 Callback URL 在 Huginn 文档中没有说明，其地址的格式如下： `https://huginn.example.com/auth/tumblr/callback`

然而，因为在访问 Huginn 之前还要通过 HTTP 基本身份验证，所以你还需要提供用户名和密码，这样他们的 OAuth 才可以获得 callback URL，请参照如下格式进行填写：`callback:password@https://huginn.example.com/auth/tumblr/callback`。

如果你愿意的话，还可以上传一个图标和应用网站的截图。填写验证码，点击 Register 按钮。你会看到你的 `OAuth key` 和 `secret`。在你的 `.env` 文件中 TUMBLR_OAUTH_KEY 和 TUMBLR_OAUTH_SECRET 填入这些，之后重启 Huginn。

登陆 Huginn，点击 Services，再点击 "Authenticate with Tumblr" 按钮，并根据提示继续操作，最后返回到 Services 界面。当你创建了一个 Tumblr Publish Agent 之后，你会发现 Service 部分会被自动填写，这时你就可以配置它。

我建议在一个新的标签页打开 [Tumblr's API documentation](https://www.tumblr.com/docs/en/api/v2#posting) 来帮助你配置 Tumblr Publish Agent。默认情况下，每一个可以配置的选项都可以在 Tumblr Publish Agent 进行配置，但是，有几个可以在 Tumblr 中进行的配置并没有它们相对应的选项，这是因为这些选项会导致 Agent 不能正确的保存，所以删除了这些不必要的选项。如果有任何问题，请使用默认设置。

# 调试 OAuth 应用

## 调试 Twitter

### Twitter error: Invalid Status code 420

假如你在本地或者云端同时使用同样的 Twitter 凭证，或者偶然使用多线程运行多个实例，或者是多线程没有及时地关闭（也会造成多个副本实例在运行），这些都有可能会导致 420 错误。

> 本文由 [Huginn 中文网](http://huginn.cn) 翻译，已经获得项目作者授权，原文请访问：[Configuring OAuth applications](https://github.com/cantino/huginn/wiki/Configuring-OAuth-applications)。


