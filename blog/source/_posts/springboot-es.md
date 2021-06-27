---
title: SpringBoot + ES
updated: 2021-06-27 00:24:14
description: SpringBoot整合ES
tags:
- Java
- SpringBoot
- elasticsearch
# 置顶优先级 数值越大优先级越高 #
sticky: 9820
cover: https://i.loli.net/2021/06/27/qLjkANQSFeuRmwW.jpg
top_img: https://i.loli.net/2021/06/25/fFpQi2wJKyVLduS.jpg
# 【可选】文章分类 #
categories: SpringBoot
# 【可选】文章关键字 #
keywords:
- ES
- elasticsearch
date: 2019-08-28 15:43:10
---

## 版本

![](https://i.loli.net/2021/06/27/CiLXfjyWmNJ2zUP.png)
{% note warning no-icon %}
尝试过高版本的7.3.1和低版本的2.4.4，都不行
{% endnote %}

> 最终选择适合SpringBoot 2.1.3版本的es是`5.6.16`！

{% note warning no-icon %}
尝试过高版本的7.3.1和低版本的2.4.4，都不行
{% endnote %}

{% note blue 'fas fa-bullhorn' modern %}
要不然`高版本`报错：`None of the configured nodes are available`
`低版本`相对友好一些，会报：`Received message from unsupported version: \[5.6.0\] minimal compatible versio` 提示该换版本了
{% endnote %}

---
## 项目

{% btn 'https://github.com/zby123456/Spring-Boot.git',GitHub项目传送门,far fa-hand-point-right,green larger %}
![](https://i.loli.net/2021/06/27/yKnVXhJYHfFCSc2.png)
## 详细
### 代码

![](https://i.loli.net/2021/06/27/cKNYWxUQMIuoBG6.png)
#### controller

![](https://i.loli.net/2021/06/27/1XhCblYpL9Hdk7V.png)
#### service

![](https://i.loli.net/2021/06/27/meuoYIzJGWtpvBX.png)
#### dao

部分demo截图：

![](https://i.loli.net/2021/06/27/4wDjC3kcVUIWaxl.png)
#### 增加数据

![](https://i.loli.net/2021/06/27/BroelUTwuiDAmKf.png)
#### 根据title来搜索

![](https://i.loli.net/2021/06/27/jgODTNw1PqhUpry.png)
#### 索引名和type

> `127.0.0.1:9200/articleindex/article/_search` 检索所有

![](https://i.loli.net/2021/06/27/EMiWqFo4VltUOXN.png)
