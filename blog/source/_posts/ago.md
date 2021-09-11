---
title: Communications link failure\n\nThe last packet successfully received from the server was 1,377,469 milliseconds ago.  The last packet sent successfully to the server was 1 milliseconds ago.
date: 2021-09-11 20:07:04
updated: 2021-09-11 20:07:04
top_img: https://i.loli.net/2021/06/25/MUH2qtVngR47pou.jpg
cover: https://i.loli.net/2021/09/11/DzgHmweEBTPqWxA.jpg
description: Communications link failure\n\nThe last packet successfully received from the server was 1,377,469 milliseconds ago.  The last packet sent successfully to the server was 1 milliseconds ago.
tags:
- SpringBoot
- MySQL
# 置顶优先级 数值越大优先级越高 #
sticky: 100003
# 【可选】文章分类 #
categories: Java
# 【可选】文章关键字 #
keywords:
- mysql
- SpringBoot
---

---

![错误日志](https://i.loli.net/2021/09/11/qDC7cFGMP9ermAV.png)

{% note danger no-icon %}
报错信息：Communications link failure\n\nThe last packet successfully received from the server was 1,377,469 milliseconds ago.  The last packet sent successfully to the server was 1 milliseconds ago.
{% endnote %}

大致意思是：
从服务器成功接收的最后一个数据包是1,377,469毫秒前。最后一个成功发送到服务器的数据包是0毫秒前。

说明这个连接已经有1,377,469毫秒没有从服务器上接收数据了，而mysql上定义，如果一个连接的空闲时间超过数据库的配置参数wait_timeout的值，MySQL将自动断开该连接，而连接池却认为该连接还是有效的(因为并未校验连接的有效性)，当应用申请使用该连接时，就会导致上面的报错。
所以，我们需要在连接池回收、借用连接或者创建连接的时候测试该连接的可用性

提供几个连接池的相关配置如下:

```yaml
spring.datasource.test-on-borrow
当从连接池借用连接时，是否测试该连接.
 
spring.datasource.test-on-connect
创建时，是否测试连接
 
spring.datasource.test-on-return
在连接归还到连接池时是否测试该连接.
 
spring.datasource.test-while-idle
当连接空闲时，是否执行连接测试.
 
spring.datasource.time-between-eviction-runs-millis
指定空闲连接检查、废弃连接清理、空闲连接池大小调整之间的操作时间间隔

```

最终配置如下：
![](https://raw.githubusercontent.com/Endless-zby/Local_img/master/20210911201717.png)
