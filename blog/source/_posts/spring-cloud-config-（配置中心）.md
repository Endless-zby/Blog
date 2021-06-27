---
title: Spring Cloud Config （配置中心）
updated: 2021-06-27 00:24:14
description: Spring Cloud Config
tags:
- Java
- SpringCloud
- Bus
# 置顶优先级 数值越大优先级越高 #
sticky: 9836
cover: https://i.loli.net/2021/06/27/YkwCFbGsfSdEi4n.jpg
top_img: https://i.loli.net/2021/06/25/fFpQi2wJKyVLduS.jpg
# 【可选】文章分类 #
categories: SpringCloud
# 【可选】文章关键字 #
keywords:
- Config
- SpringCloud
date: 2019-07-17 15:27:37
---

### 官方对Spring Cloud Config的解释：

> Spring Cloud Config provides server and client-side support for externalized configuration in a distributed system. With the Config Server you have a central place to manage external properties for applications across all environments. The concepts on both client and server map identically to the Spring `Environment` and `PropertySource` abstractions, so they fit very well with Spring applications, but can be used with any application running in any language. As an application moves through the deployment pipeline from dev to test and into production you can manage the configuration between those environments and be certain that applications have everything they need to run when they migrate. The default implementation of the server storage backend uses git so it easily supports labelled versions of configuration environments, as well as being accessible to a wide range of tooling for managing the content. It is easy to add alternative implementations and plug them in with Spring configuration.

### 个人发挥：

理解：Spring Cloud Config：`统一配置中心` ，将配置文件存放到github中，方便集中管理，并实现配置与代码相分离

![](https://i.loli.net/2021/06/27/q7XckgFaYBQWlzi.png)

如果微服务架构中没有使用统一配置中心时，所存在的问题：

- 配置文件分散在各个项目里，不方便维护
- 配置内容安全与权限，实际开发中，开发人员是不知道线上环境的配置的
- 更新配置后，项目需要重启

> 使用`config配置中心`，以上问题可以得到完美解决

### 步骤：

- 在github中创建配置中心项目：[Arsenal_Config](https://github.com/zby123456/Arsenal_Config)

![](https://i.loli.net/2021/06/27/45OmFUfwcozEd82.png)
- 将本地微服务userinfo的yaml传入github的项目中

![](https://i.loli.net/2021/06/27/oralev8bKYFdLX1.png)
### 注意
> spring cloud config约定：配置文件的命名格式 A-B.yml 或 A-B.properties  
> 因此需要在github上的application.yml重命名为userinfo-dev.yml  
> userinfo-dev.yml: userinfo 是项目的名字 ，dev是环境名  
> 文件命名格式是 A-B.yml 而不是 A_B.yml !!!!!!!!!!

### 配置

- 以上将yml放在了github上，接下来在本地项目中引用

- 创建新的子项目project\_config，并引入依赖

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-config-server</artifactId>
    </dependency>
```

- 创建启动类

![](https://i.loli.net/2021/06/27/urfc87aBVPTYpoI.png)
- 创建启动类

- 编写配置文件application.yml

```yaml
server:
  port: 10010

spring:
  application:
    name: config
  cloud:
    config:
      server:
        git:
          uri: https://github.com/zby123456/Arsenal_Config.git
```

![](https://i.loli.net/2021/06/27/34oFwfyTS2ivcOK.png)
> application.yml 中的uri使用刚才创建的 [Arsenal_Config](https://github.com/zby123456/Arsenal_Config) 仓库地址ssh或者https都行

> 在具体的微服务中（userinfo） ，通过 project_config 引用github的配置文件

### 引入依赖

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
```

- 更改配置文件：删除application.yml（已经存放在了github上）, 新增bootstrap.yml （告知程序配置存放在了github上的具体位置）

- 编写 bootstrap.yml 内容

```
spring:
  cloud:
    config:
      name: armstype
      profile: dev
      label: master
      uri: http://127.0.0.1:10010
```

![](https://i.loli.net/2021/06/27/9DtTFnxgEP4damG.png)
> (一般约定：本地的独立配置使用application.yml/application.properties,如果是要引入 github上的配置，则本地配置称为bootstrap.yml)  
> 优先级问题：`bootstrap.yml > application.yml`

### 测试

- 在git上的userinfo-dev中加入测试数据（数据库相关配置没有粘出来！）

```yaml
server:
  port: 9001

eureka:
  client:
    serviceUrl:
      defaultZone: http://127.0.0.1:9999/eureka
  instance:
    prefer-ip-address: true
zby:
  name: 赵博雅
  age: 21
  sex: 男
```

- 在controller中编写测试类，这里方便观察结果，将信息显示在页面上

```java
package com.zby.controller;

import com.zby.client.client;
import com.zby.entity.Result;
import com.zby.entity.User;
import com.zby.service.UserService;
import com.zby.util.IdWorker;
import com.zby.util.JwtUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.util.HashMap;


//@RefreshScope
@Controller
@RequestMapping("user")
public class UserController {

    @Value("${zby.name}")
    private String name;

    @Value("${zby.age}")
    private String age;

    @Value("${zby.sex}")
    private String sex;

/**
     * config测试方法
     * @return
     */
    @GetMapping("config")
    public ModelAndView Config(){
        HashMap<String, String> map = new HashMap<String, String>();
        map.put("name",name);
        map.put("age",age);
        map.put("sex",sex);
        Result result = new Result(true, 20000, "成功", map);
        return new ModelAndView("config_test","result",result);
    }
}
```

- 启动三个服务：Eureka、Config、userinfo

![](https://i.loli.net/2021/06/27/Swb3H1Xzela7Yp8.png)

### 问题

如果直接修改git中的配置文件，在不重启服务的情况下能不能生效？

### 解答

不能，那如何实现

### 解决方案

使用Spring Cloud Bus ( 消息总线 )

只需要在Spring Cloud Config Server端发出refresh,就可以触发所有微服务更新了
