---
title: Spring Cloud Config：统一配置中心
tags: []
id: '283'
categories:
  - - java
  - - SpringBoot
date: 2019-05-05 17:11:36
---

将配置文件存放到github中，方便集中管理，并实现配置与代码相分离

**_1、在github中创建配置中心项目：MavenProject\_config_**

![](https://www.zby123.club/wp-content/uploads/2019/05/KXUYOQ2PXLRU2AJHNBQ.png)

config目录结构

启动类：

```
package com.zby.config;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;
@EnableConfigServer
@SpringBootApplication
public class ConfigApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigApplication.class);
    }
}
```

application.yml：

```
server:
  port: 10010
spring:
  application:
    name: MavenProject-Config
  cloud:
    config:
      server:
        git:
          uri: （填写git上创建的存放配置文件的仓库地址）
  rabbitmq:
    host: （rabbitmq的服务地址）
management:
  endpoints:
    web:
      exposure:
        include: bus-refresh
```

引入jar包：

```
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
```

**_2、在具体的微服务中（MavenProject-active） ，通过_**  
**_MavenProject_** **_\_config 引用github的配置文件_**

**_3、引入依赖_**

```
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
```

**_4、更改配置文件：_**  
删除application.yml（已经存放在了github上）, 新增bootstrap.yml （告知程序配置存放在了github上的具体位置）

  
优先级问题：bootstrap.yml > application.yml  

一般约定：本地的独立配置使用application.yml/application.properties；  
如果是要引入 github上的配置，则本地配置称为bootstrap.yml  

![](https://www.zby123.club/wp-content/uploads/2019/05/4H35WA4GQUXF8K149FH2-1024x578.png)

**_5、测试_**  
a.启动Eureka、Config、Active三个项目，测试能否在Base中访问数据库 -->正常是可以的  
b.测试在github上修改 Active-dev 的yml，查看本地是否立刻生效 ------>不可以。

**_问题：_**当每次重启后修改的参数才会生效，那么如何在服务不重启的情况下使更改的配置生效？  
**_解决方案：使用_****Spring-Cloud-Bus**（**消息总线**）

基本思路：

a->bus->b  
修改文件->bus->让base项目立刻生效 ，其中底层在传递数据时 使用到 MQ

**_1、MavenProject \_config 中引入依赖：_**

```
        <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-bus</artifactId>
        </dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-stream-binder-rabbit</artifactId>
</dependency>
```

_**2、在 MavenProject \_config 的yml中配置RabbitMQ和Bus**_

```
  rabbitmq:
    host: （rabbitmq服务地址）
management:
  endpoints:
    web:
      exposure:
        include: bus-refresh
```

**_3、在微服务 MavenProject-active 中引入相关依赖：_**

```
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

**_再次测试：_**

启动eureka、config、 active 项目，修改github上的yml ，不重启本地服务，尝试修改是否生效 ------>不生效

  
还差最后一步：通过Post访问，手工告知程序 生效：  
**_Post_**: **http://127.0.0.1:10010/actuator/bus-refresh**

此方法只适用于使更改过的yml配置文件立即生效，而不用重启服务

但是，yml有两个功能 **配置**和**数据注入**

如果要使数据注入也同步更新需要在controller方法上加上**@RefreshScope**注解

_**ok!**_