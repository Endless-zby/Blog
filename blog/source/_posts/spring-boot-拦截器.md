---
title: Spring boot 拦截器
tags: []
id: '246'
categories:
  - - java
  - - SpringBoot
date: 2019-04-23 22:27:17
---

Spring boot 2.0 之后只需要继承WebMvcConfigurationSupport类，重写add  
Intercepto 方法添加注册拦截器即可完成拦截  
继承HandlerInterceptorAdapter重写preHandle方法来完成拦截操作。

> _这里以发布评论为例，在发送利用拦截器（token）进行身份校验_

创建拦截类

```
package com.zby.qa.config;
import com.zby.qa.interceptor.JwtInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;
@Component
@Configuration
public class JwtConfiguration extends WebMvcConfigurationSupport {
    @Autowired
    JwtInterceptor jwtInterceptor;
    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
/**
 * addInterceptor ：添加拦截方法
 * addPathPatterns ：添加拦截请求路径（/** ：拦截一切请求）
 * excludePathPatterns ：加入白名单（此请求不拦截）
 * */
    registry.addInterceptor(jwtInterceptor).addPathPatterns("/**")
            .excludePathPatterns("");
    }
}
```

重写拦截方法

```
package com.zby.qa.interceptor;
import com.zby.util.JwtUtil;
import io.jsonwebtoken.Claims;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
@Component
public class JwtInterceptor extends HandlerInterceptorAdapter {
    @Autowired
    JwtUtil jwtUtil;
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("此处拦截器已拦截，正在校验当前登录者的身份");
        //获取页面头Authrorization
        String authrorization = request.getHeader("Authrorization");
        if(authrorization != null && authrorization.startsWith("Bearer ")){
            //截取token段
            String token = authrorization.substring(7);
            //解析token
            Claims claims = jwtUtil.parseJwt(token);
            if(claims != null){
                if("1".equalsIgnoreCase((String) claims.get("roles"))){
                    request.setAttribute("access_admin",claims);
                }else if("0".equalsIgnoreCase((String) claims.get("roles"))){
                    request.setAttribute("access_error",claims);
                }else {
                    throw new RuntimeException("操作拒绝！");
                }
            }
            return true;
        }
        return true;
    }
}
```

Service（部分测试代码）

![](https://www.zby123.club/wp-content/uploads/2019/04/O_7HEGJRO7N0@AW2J7UP-1024x352.png)

Controller （部分测试代码）

![](https://www.zby123.club/wp-content/uploads/2019/04/57JY7K76IMUBZGQYN4R-1024x341.png)

测试（没权限）：

![](https://www.zby123.club/wp-content/uploads/2019/04/1-1024x587.png)

![](https://www.zby123.club/wp-content/uploads/2019/04/2-1024x379.png)

![](https://www.zby123.club/wp-content/uploads/2019/04/3-1024x333.png)

![](https://www.zby123.club/wp-content/uploads/2019/04/4-1024x144.png)

测试（有权限）：

![](https://www.zby123.club/wp-content/uploads/2019/04/1-1-1024x508.png)

![](https://www.zby123.club/wp-content/uploads/2019/04/2-1-1024x317.png)

![](https://www.zby123.club/wp-content/uploads/2019/04/3-1-1024x165.png)