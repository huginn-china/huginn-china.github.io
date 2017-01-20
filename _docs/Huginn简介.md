---
title: Huginn 简介
layout: docs
permalink: /docs/Huginn简介.html
nextPage: 在Ubuntu-Debian上安装Huginn.html
---

Huginn 是一个用于构建在线执行自动化任务的系统，它可以监控网页变化、聚合第三方应用并且根据设定的触发条件执行自动化操作，你可以将它看作是一个可以运行在自己服务器上的 Yahoo!Pipes + IFTTT。该工具的作者为 [Andrew Cantino](http://andrewcantino.com/)，Github 上的项目地址为 https://github.com/cantino/huginn/， 已经有上万的 star，而且该项目活跃度非常高，一直都有人在更新代码。

> Yahoo!Pipes 是 雅虎提供一个信息聚合服务，可以用来烧制 RSS，该服务已于 2015 年 9 月 30 日完全关闭。

> [IFTTT](https://ifttt.com/) 是 "if this then that"，意指『如果这样，则那样』，即如果一件事情发生了，则自动触发另一件事情。 

![huginn 的传说](https://raw.githubusercontent.com/cantino/huginn/master/doc/imgs/the-name.png)

> 项目名称的由来：在北欧神话中，奥丁的肩膀上坐着两只乌鸦，一只名叫 Huginn，一只名叫 Muninn。这两只乌鸦告诉奥丁他们的所见所闻，毫无遗漏。奥丁在黎明时派出它们，它们飞遍全世界然后在晚餐之前回来汇报，因此，奥丁能知晓很多事情。

##  Huginn 能实现的功能：

+ 跟踪天气的变化，如果监测到明天要下雨或下雪，就会发邮件提醒你：别忘了带伞哦！

+ 列出你关心的事项，并且当他们在微博上发生变化的时候自动接收邮件。（例如：如果你想知道全世界在“机器学习”方面发生的趣闻，那么 Huginn 可以监听微博上所有含有“机器学习”的条目，并且在发生尖锐的讨论时告诉你）

+ 监测航班行程和购物订单信息。

+ 在微博上关注你的项目名称，并且在人们提到它们时自动更新。

+ 抓取网页内容并且在它们发生变化时发送邮件给你。

+ 可以与 Adioso, HipChat, Basecamp, Growl, FTP, IMAP, Jabber, JIRA, MQTT, nextbus, Pushbullet, Pushover, RSS, Bash, Slack, StubHub, translation APIs, Twilio, Twitter, Wunderground, and Weibo等第三方服务连接。

+ 在一天中的某个指定时间发送通知邮件给你，告知你需要知道的事情。

+ 追踪高频次事件的数量，并且在出现突发状况时发送短信给你，就像“旧金山紧急报道”。

+ 发送和接收 Webhooks(网络钩子，Webhook 就是用户通过自定义回调函数的方式来改变Web应用的一种行为）

+ 运行自定义的 JavaScript 或者 CoffeeScript 函数

+ 追踪你的位置轨迹

+ 创建AMT工作流（Amazon Mechanical Turk workflows）代理的输入或输出。（Amazon Turk Agen

  被称为 “HumanTaskAgent” --人类任务代理）。例如，每天向5个人要一张有趣的猫的图片，然后将这些图片发送给5个或更多人去评分，然后发送评分数最高的照片给5个人并向他们征集有趣的标题，然后将这些标题发送给5个人让他们评出最有趣的标题，最后将最有趣的标题和图片发布到我的博客里。
  
## Huginn 中的主要概念

与 IFTTT 通过 Recipe 一样，Huginn Agent 可以创建、利用事件，并沿着一条定向的路径来传播（事件流）。你能时刻把握你的数据的动态。

## 加入我们

加入我们的[Slack](https://huginn-china.slack.com/messages/general/) 来共同讨论该项目（huginn-china），或者你可以加入原作者的 [Gitter room](https://gitter.im/cantino/huginn)。

想用 Huginn 来帮助别人？我们鼓励所有形式的贡献！你可以贡献丰富的 UI 组件，或者制作一个新的 Agent 分享给大家，或者编写文档来指导那些对 Huginn 不太熟悉的人。或者，你可以挖掘或提出程序存在的问题，欢迎 fork 这个仓库，做出修正，并推送给我们。


