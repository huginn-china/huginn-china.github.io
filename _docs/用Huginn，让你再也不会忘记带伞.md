---
title: 用 Huginn，让你再也不会忘记带伞
layout: docs
permalink: /docs/用Huginn，让你再也不会忘记带伞.html
prevPage: network-image-node.html
nextPage: map-node.html
---

[Huginn](https://github.com/cantino/huginn) 是一年来我在广大开源协作者的支持下一直在做的一款工具。Huginn 是建立数据采集和数据反应日常生活的任务的轻型基础设施,把它看成是一个开源的 Yahoo! Pipes，IFTTT，或 Zapier。如果，在未来，Huginn 演变成类似 Google Now，但没有潜在的因素，因为您可以控制​​和主机自己的数据，它不会让我感到吃惊。

我没有做过的所有共享，可以与 Huginn 建的事情了很好的工作，但我决心开始。

因此，在这篇文章中，一个很简单的例子：

**问题：** 我总是忘记查看天气，忘记带伞，被淋成落汤鸡。
**解决方案：** 当你所在地区有雨的时候，Huginn 每天早晨会向您发送电子邮件（或短信）。

在服务器上已经安装 Huginn，制作一个新的天气 Agent：

![](https://ws1.sinaimg.cn/large/70b88a6bjw1f602jxqdogj20of03edg2.jpg)

你只需要将自己邮政编码（或位置代码）和 Wunderground 一个免费的API密钥。设置代理在晚上10点运行，这样它收集的数据是明天的天气。

![](https://ws2.sinaimg.cn/large/70b88a6bjw1f602m5nr7aj21iq128124.jpg)

一旦你保存你的新WeatherAgent，现在是时候来建立一个TriggerAgent。你需要把你刚才创建的WeatherAgent设置为源。

![](http://ww2.sinaimg.cn/large/70b88a6bjw1f602n8393nj21hg14ggxj.jpg)

TriggerAgents 包含一套规则，所有这一切都必须为触发火灾匹配。在这种情况下，我们只需要一个规则来匹配该Wunderground API天气事件的条件属性。我们会做一个正则表达式（正则表达式）对阵此属性，寻找写着“雨”或“风暴”。当触发火灾，将输出与消息的事件“带把雨伞！它看起来像“<条件>'明天'<位置>'”。这些<...>字段将通过从事件在这种情况下是filtered-，从您刚才WeatherAgent事件相应的属性填写。该截图显示了代理的选项正在被编辑为JSON，使其更容易为你阅读。您可以使用编辑器模式。

最后，我们只需要代理给我们发电子邮件的Trigger Agent的事件。我们将使用一个DigestEmailAgent。它可以在一个特定的时间，将它收到的事件一次性地使用Email Agent发送电子邮件，或者wilioAgent发送短信。

设置的事件的日程安排在早上6点运行，这样你醒来就可以收到电子邮件。记得为你刚刚创建的下雨警报事件设置来源。

![](http://ww3.sinaimg.cn/large/70b88a6bjw1f602r69lalj21hu0ve0zs.jpg)

就是这样！现在，您会在早上6点的早晨，当雨水在你的面积预计收到一封电子邮件。保持干爽！

我会尽快发布更多的例子。你想看到什么样的Huginn教程，告诉我吧！


> 本文由 [Huginn 中文网](http://huginn.cn) 翻译，已经获得项目作者授权，项目原文访问 [Never Forget Your Umbrella Again, With Huginn](http://blog.andrewcantino.com/blog/2014/01/12/never-forget-your-umbrella-again-with-huginn)

