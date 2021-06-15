---
title: RedisTemplate的高级应用
tags: []
id: '584'
categories:
  - - java
  - - SpringBoot
date: 2019-08-01 20:32:28
---

> 整理了一些关于RedisTemplate的常见用法

整合上次学习到的Swagger来做一个关于 RedisTemplate 的实战环境

代码不难，就不贴了，需要的话去Git搞 [https://github.com/zby123456/Arsenal/tree/master/testall](https://github.com/zby123456/Arsenal/tree/master/testall)

![](https://zby123.club/wp-content/uploads/2019/08/redis1.png)

写的时候正在更新

实战环境就是把这个测试的微服务打包docker镜像扔到服务器上，脱离了本地的tomcat配置，但是过程是真的恶心人，有时间再把如何在本地打包镜像以及如何部署镜像在服务器的docker中详细的说一下

关于docker的安装使用看另一篇文章 (往下翻翻就能看到)[https://zby123.club/index.php/2019/07/05/docker/](https://zby123.club/index.php/2019/07/05/docker/)

好了，话不多说点击传送门，Go!!!

[传送门](http://39.96.160.110:9005/swagger-ui.html)

(因为我懒得在项目搞SSL，所以这个网页会提示不安全，放心访问)