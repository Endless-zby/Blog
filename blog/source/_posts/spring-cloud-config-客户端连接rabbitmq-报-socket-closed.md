---
title: Spring cloud config 客户端连接RabbitMQ 报 socket closed
tags: []
id: '630'
categories:
  - - 解决各种不爽
date: 2019-08-07 10:23:13
---

![](https://zby123.club/wp-content/uploads/2019/08/报错1-1024x556.png)

修改端口为5672

放开服务器端口：

![](https://zby123.club/wp-content/uploads/2019/08/端口-1024x337.png)

原因：rabbitmq 服务端口是5672，15672是其客户端