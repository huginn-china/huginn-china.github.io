---
title: 调试 Agent
layout: docs
permalink: /docs/调试Agent.html
prevPage: network-image-node.html
nextPage: map-node.html
---

假如你写了一个非常酷的 Agent，该 Agent 在彗星撞地球时产生事件，但是上一次彗星撞地球时，该 Agent 发生了错误，它没有产生事件。所以，如果你不想下一次再发生错误，最好的方法就是去调试。

如果你认为你的 Agent 有 bug， log() 或 errors.add() 是很有用的。然后在 web 页面上手动运行你的 Agent(Actions -> Run)， 检查 Log 信息 (Actions -> Show -> Logs).

你也可以用一个 ManualEvent Agent 去手动触发事件运行。

最后，所有的日志消息和错误信息都可以在 huginn/log/development.log 文件中查看，但是从这个文件中很难筛选出单个 Agent 的 log 信息。

> 本文由 [Huginn 中文网](http://huginn.cn) 翻译，已经获得项目作者授权，项目原文访问 [Debugging an agent](https://github.com/cantino/huginn/wiki/Debugging-an-agent)

