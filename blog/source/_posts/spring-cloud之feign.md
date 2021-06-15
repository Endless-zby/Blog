---
title: Spring Cloud之Feign
tags: []
id: '402'
categories:
  - - java
  - - SpringBoot
date: 2019-07-11 13:20:18
---

### Feign的目标

feign是声明式的web service客户端，它让微服务之间的调用变得更简单了，类似controller调用service。Spring Cloud集成了Ribbon和Eureka，可在使用Feign时提供负载均衡的http客户端。

### Feign原理简述

*   启动时，程序会进行包扫描，扫描所有包下所有@FeignClient注解的类，并将这些类注入到spring的IOC容器中。当定义的Feign中的接口被调用时，通过JDK的动态代理来生成RequestTemplate
*   RequestTemplate中包含请求的所有信息，如请求参数，请求URL等。
*   RequestTemplate声场Request，然后将Request交给client处理，这个client默认是JDK的HTTPUrlConnection，也可以是OKhttp、Apache的HTTPClient等。
*   最后client封装成LoadBaLanceClient，结合ribbon负载均衡地发起调用。

### 引入Feign

使用Maven来管理相关jar

```
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
```

当然，前提是已经有了注册中心Eureka（贴上 Eureka ）

```
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId> spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
```

armstype 的yml：

```
server:
  port: 9000

eureka:
  client:
    serviceUrl:
      defaultZone: http://127.0.0.1:9999/eureka
  instance:
    prefer-ip-address: true

spring:
  application:
    name: armstype

  datasource:
    driverClassName: com.mysql.jdbc.Driver
    url: jdbc:mysql://cdb-6kot9g5e.cd.tencentcdb.com:10007/arms?characterEncoding=utf-8&useSSL=true
#    url: jdbc:mysql://localhost:3306/arms?characterEncoding=utf-8
    username: root
    password: zby123456

  thymeleaf:
    cache: false
    prefix: classpath:/templates/
    suffix: .html
    encoding: UTF-8
    mode: HTML5

  jpa:
    database-platform:
      # spring boot 2.0 的坑， spring boot2.+后默认使用的是MyISAM引擎
      org.hibernate.dialect.MySQL5InnoDBDialect
    database: MySQL
    generate-ddl: true
    show-sql: true

    hibernate:
      ddl-auto: update

## 添盐加密
#jwt:
#  config:
#    key: zby      # 盐
#    expire: 120000       #过期时间
```

userinfo 的yml：

```
server:
  port: 9001

eureka:
  client:
    serviceUrl:
      defaultZone: http://127.0.0.1:9999/eureka
  instance:
    prefer-ip-address: true

spring:
  application:
    name: userinfo

  datasource:
    driverClassName: com.mysql.jdbc.Driver
    url: jdbc:mysql://cdb-6kot9g5e.cd.tencentcdb.com:10007/arms?characterEncoding=utf-8&useSSL=true
#    url: jdbc:mysql://localhost:3306/arms?characterEncoding=utf-8
    username: root
    password: zby123456

  thymeleaf:
    cache: false
    prefix: classpath:/templates/
    suffix: .html
    encoding: UTF-8
    mode: HTML5

  jpa:
    database-platform:
      # spring boot 2.0 的坑， spring boot2.+后默认使用的是MyISAM引擎
      org.hibernate.dialect.MySQL5InnoDBDialect
    database: MySQL
    generate-ddl: true
    show-sql: true

    hibernate:
      ddl-auto: update

# 添盐加密
jwt:
  config:
    key: zby      # 盐
    expire: 120000       #过期时间


```

实例目的： 在userinfo的微服务中调取armstype中的test()方法

上面我们已经引入了相关依赖，这里我们声明哪些服务作为Eureka的客户端存在、有哪些服务可以在Eureka中可以被发现，两个注解搞定

@EnableDiscoveryClient（开启“在注册中心发现其他微服务”的功能）
@EnableFeignClients（注册本服务为客户端）

![](https://zby123.club/wp-content/uploads/2019/07/Feign1-1024x491.png)

发现服务和被发现服务都开启这两个注释

armstype中被调用的方法：

```
    @ResponseBody
    @GetMapping("test")
    public String test(){
        return "12345";
    }
```

在userinfo服务中创建接口文件里链接到 armstype 服务

```
package com.zby.client;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;

/**
 * @Author: 赵博雅
 * @Date: 2019/7/5 10:34
 */
@Component
@FeignClient(name = "armstype")//uereka中的注册服务名
public interface client {
    
    @GetMapping(value = "arsenal/view")//原方法执行路径
    ModelAndView view(HttpServletRequest request);

    @GetMapping("arsenal/test")
    String test();

}
```

Controller：

```
package com.zby.controller;

import com.zby.client.client;
import com.zby.entity.Result;

import com.zby.entity.User;
import com.zby.service.UserService;

import com.zby.util.IdWorker;
import com.zby.util.JwtUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@RequestMapping("user")
public class UserController {

    @Autowired
    private client client;

    /**
     * 跨服务调用
     * userinfo---->armstype
     */
    @ResponseBody
    @GetMapping(value = "test")
    public String test(){
        return client.test();
    }
}
```

![](https://zby123.club/wp-content/uploads/2019/07/Feign2.png)

可以看到访问地址中的端口是 userinfo 服务的端口，但是依然通过Feign来执行了 armstype 中的test()方法