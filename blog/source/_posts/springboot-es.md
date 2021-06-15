---
title: SpringBoot + ES
tags: []
id: '741'
categories:
  - - java
  - - SpringBoot
date: 2019-08-28 15:43:10
---

版本！

![](https://zby123.club/wp-content/uploads/2019/08/image-7-1024x287.png)

尝试过高版本的7.3.1和低版本的2.4.4，都不行

最终选择适合SpringBoot 2.1.3版本的es是5.6.16！

要不然高版本报错：None of the configured nodes are available

低版本相对友好一些，会报：Received message from unsupported version: \[5.6.0\] minimal compatible versio 提示该换版本了

代码地址：https://github.com/zby123456/Spring-Boot.git

![](https://zby123.club/wp-content/uploads/2019/08/elasticsearch9-1024x556.png)

搜索：

![](https://zby123.club/wp-content/uploads/2019/08/code1-1024x149.png)

controller

![](https://zby123.club/wp-content/uploads/2019/08/code2-1024x158.png)

service

![](https://zby123.club/wp-content/uploads/2019/08/code3-1024x164.png)

dao

部分demo截图：

![](https://zby123.club/wp-content/uploads/2019/08/elasticsearch10-1024x601.png)

增加数据

![](https://zby123.club/wp-content/uploads/2019/08/elasticsearch11-1024x631.png)

根据title来搜索

![](https://zby123.club/wp-content/uploads/2019/08/image-8-1024x479.png)

索引名和type

127.0.0.1:9200/articleindex/article/\_search 检索所有

![](https://zby123.club/wp-content/uploads/2019/08/elasticsearch12-1024x607.png)

\_search ：查看所有节点