---
title: Spring Cloud Feign 之fallback（熔断机制）
tags: []
id: '469'
categories:
  - - SpringBoot
date: 2019-07-17 09:40:03
---

### FeignClient注解的一些属性

属性

默认值

作用

备注

value

空字符串

调用服务名称，和name属性相同

serviceId

空字符串

服务id，作用和name属性相同

已过期

name

空字符串

调用服务名称，和value属性相同

url

空字符串

全路径地址或hostname，http或https可选

decode404

false

配置响应状态码为404时是否应该抛出FeignExceptions

configuration  
  

{}

自定义当前feign client的一些配置

参考FeignClientsConfiguration

fallback

void.class

熔断机制，调用失败时，走的一些回退方法，可以用来抛出异常或给出默认返回数据。

底层依赖hystrix，启动类要加上@EnableHystrix

path

空字符串

自动给所有方法的requestMapping前加上前缀，类似与controller类上的requestMapping

primary

  
true

跨服务调用以下方法

重点：@FeignClient(name = "armstype",fallback = clientImpl.class)

```java
package com.zby.client;

import com.zby.client.clientImpl.clientImpl;
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
@FeignClient(name = "armstype",fallback = clientImpl.class)//uereka中的注册服务名，
public interface client {

    @GetMapping(value = "arsenal/view")//原方法执行路径
    ModelAndView view(HttpServletRequest request);

    @GetMapping("arsenal/test")
    String test();
}
```

写熔断机制

```java
package com.zby.client.clientImpl;

import com.zby.client.client;
import com.zby.entity.Result;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.ModelAndView;
import javax.servlet.http.HttpServletRequest;

/**
 * @Author: 赵博雅
 * @Date: 2019/7/16 15:58
 */
@Component
public class clientImpl implements client {

    public ModelAndView view(HttpServletRequest request) {
        Result result = new Result(false, 20004, "请求失败，服务器无响应,熔断机制启动保护", null);
        return new ModelAndView("404","result",result);
    }

    public String test() {
        return "请求失败，服务器无响应,熔断机制启动保护";
    }
}
```

测试效果，启动eureka以及调用服务（不启动被调用服务）模拟被调用服务不在线状态

![](https://zby123.club/wp-content/uploads/2019/07/熔断器1-1024x556.png)

一个服务在线

![](https://zby123.club/wp-content/uploads/2019/07/熔断器2-1024x556.png)

测试

完成