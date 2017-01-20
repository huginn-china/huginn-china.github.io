---
title: 第三方应用服务使用 Webhook Agent 连接 Huginn
layout: docs
permalink: /docs/第三方应用通过WebhookAgent连接Huginn.html
prevPage: network-image-node.html
nextPage: map-node.html
---

一般地，通过使用 Webhook Agent，你可以将任意数据导入进 Huginn 中进行加工处理。当数据通过 POST 的方式发送给 Webhook Agent 时，将会产生一个或多个事件，同时触发其他的 Agents。你可以把 Webhook  Agent 当作成普通的 REST API 端点，通过触发这样的端点来实现相应的功能。在实践中，我们通过 POST 一个 JSON 文件给 Webhook Agent，利用相应的键-值就可以在 Huginn 中完成相应的操作。

### Webhook Agent URLs

Webhook Agent 产生的链接（URL）的大致是这样的：`https://huginn.example.com/users/1/web_requests/948/neverGonnaGiveYouUp`，下面对该 URL 做以下几点解释：
* URL 中的 `/users/1` 部分代表的是 Huginn 用户，`/users/1` 指的是 Huginn 网站的管理员（admin），`/users/2` 指的是创建的第二个用户，其他的以此类推。
* `/web_requests/` 可以认为是 Webhook Agent 的 REST API rail。 
* `/948/` 代表的是 Huginn agent 的编号（这里的 agent 编号为948），对于不同的 Webhook Agent，该数字也是不同的。
* 在 Webhook Agent 的配置中，`secret` 相当于 API key，用来认证来着外部的连接。针对不同的 Webhook Agent，最好使用不同的 `secret`，以防被其他人识破，这相对于一个密码。
* 假如从外网登录你的 Huginn 网站需要通过反向代理和 HTTP 授权验证，那样的话，此时的链接应该类似下面这样：
`https://webhookuser:webhookpassword@huginn.example.com/users/1/web_requests/948/neverGonnaLetYouDown`
* 你的外部连接也可以来自与 Huginn 网站的同一主机，这样的话，就不需要担心 HTTP 授权的问题： `http://localhost:3000/users/1/web_requests/948/neverGonnaRunAroundAndDesertYou`

### 向 Webhook Agent 发送请求

目前，Webhook Agent 支持下面两种 HTTP 方式：
* POST（默认）
* GET

**通过 POST 方式连接 Webhook Agent 的必须是 JSON 格式的文件，所有向 Webhook Agent 的 HTTP 请求必须带有`Content-Type: application/json`的 HTTP header，否则可能会产生一些奇怪的错误。**

### 使用案例：

假如你需要在 python 中与 Webhook Agent 进行交互，使用 [Requests](http://docs.python-requests.org/en/latest/) 模块进行请求，则可以参照下面的代码：

```
search_results = ["https://www.example.com/", "http://site.example.com"]
custom_headers = { "Content-Type": "application/json" } # HTTP header
results = { "results": search_results }
request = requests.post(webhook, data=json.dumps(results), headers=custom_headers) # 通过requests模块实现 POST 请求
```

> 本文由 [Huginn 中文网](http://huginn.cn) 翻译，已经获得项目作者授权，项目原文访问 [Using a Webhook Agent with external software](https://github.com/cantino/huginn/wiki/Using-a-Webhook-Agent-with-external-software)

