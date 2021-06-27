---
title: Spring Cloud Bus (消息总线)
updated: 2021-06-27 00:24:14
description: Spring Cloud Bus
tags:
- Java
- SpringCloud
- Bus
# 置顶优先级 数值越大优先级越高 #
sticky: 9835
cover: https://i.loli.net/2021/06/27/YkwCFbGsfSdEi4n.jpg
top_img: https://i.loli.net/2021/06/25/fFpQi2wJKyVLduS.jpg
# 【可选】文章分类 #
categories: SpringCloud
# 【可选】文章关键字 #
keywords:
- Bus
- SpringCloud
date: 2019-07-18 18:11:17
---

### Spring Cloud 对Bus的解释：

> Spring Cloud Bus links nodes of a distributed system with a lightweight message broker. This can then be used to broadcast state changes (e.g. configuration changes) or other management instructions. The only implementation currently is with an AMQP broker as the transport, but the same basic feature set (and some more depending on the transport) is on the roadmap for other transports.

### 个人发挥：

> 理解： 让所有为服务来订阅这个事件，当这个事件发生改变了，就可以通知所有微服务去更新它们的内存中的配置信息。这时Bus消息总线就能解决，你只需要在springcloud Config Server端发出refresh，就可以触发所有微服务更新了。

![](https://i.loli.net/2021/06/27/cSqECfeK298jz6O.png)
> Spring Cloud Bus

### 步骤：

- 在config微服务中引入依赖（需要使用到mq，不懂的话移步Docker）

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-bus</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-stream-binder-rabbit</artifactId>
    </dependency>
```

- 保证mq启动

![](https://i.loli.net/2021/06/27/ZX8esL6T1QRAKf2.png)
- 在yml中配置mq

```yaml
rabbitmq:
    host: 127.0.0.1（rabbitmq服务地址）
management:
  endpoints:
    web:
      exposure:
        include: bus-refresh
```

- 在userinfo微服务中引入依赖

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-bus</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-stream-binder-rabbit</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

- 在[Arsenal_Config](https://github.com/zby123456/Arsenal_Config)/userinfo-dev.yml中添加mq配置

![](https://i.loli.net/2021/06/27/fm6TXSWQAbgnLcx.png)
- 代码块需要注意

![](https://i.loli.net/2021/06/27/N2hD1Vnp8PcLzHT.png)
`@RefreshScope`注解的作用: 如果刷新了bean，那么下一次访问bean(即执行一个方法)时就会创建一个新实例。

### 测试：

- 启动Eureka、Config、userinfo三个微服务

![](https://i.loli.net/2021/06/27/Owzimvcd8BZF3uK.png)
[http://127.0.0.1:9001/user/config](http://127.0.0.1:9001/user/config)

- 去Git的配置中心修改其中的一些参数

![](https://i.loli.net/2021/06/27/ZtbIPfuTU8mOXGy.png)
- 更新

![](https://i.loli.net/2021/06/27/4INGywYcpRTgC5Z.png)
> 当前post请求是刷新了所有微服务的配置文件Spring Cloud Bus对这种场景也有很好的支持：`/bus/refresh`接口还提供了`destination`参数，用来定位具体要刷新的应用程序。比如，我们可以请求`/bus/refresh?destination=服务名字:9000`，此时总线上的各应用实例会根据`destination`属性的值来判断是否为自己的实例名

- 在不重启服务的情况下刷新一下页面

![](https://i.loli.net/2021/06/27/WrifRsdJk5cv3OV.png)
ok  
可以看到打印信息已经改变
