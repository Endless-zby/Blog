---
title: SpringBoot中拦截器拦截静态资源问题（thymeleaf）
updated: 2021-06-27 00:24:14
description: SpringBoot中拦截器拦截静态资源问题（thymeleaf）
tags:
- Java
- SpringBoot
# 置顶优先级 数值越大优先级越高 #
sticky: 9838
cover: https://i.loli.net/2021/06/27/YkwCFbGsfSdEi4n.jpg
top_img: https://i.loli.net/2021/06/25/fFpQi2wJKyVLduS.jpg
# 【可选】文章分类 #
categories: SpringBoot
# 【可选】文章关键字 #
keywords:
- Config
- SpringBoot
date: 2019-07-11 11:02:57
---

> SpringBoot2.0拦截器WebMvcConfigurationSupport

### 错误如图所示

- 控制台信息
  ![](https://i.loli.net/2021/06/27/OjGx2dTBL1ERgMb.png)

- 页面测试
  ![](https://i.loli.net/2021/06/27/tg9VmihzdoHl1r2.png)


### 解决方案

- 添加静态资源处理器来过滤掉静态资源，使静态资源的请求不被拦截器拦截

- 看一下 `WebMvcConfigurationSupport` 中关于静态资源的方法

> addResourceHandlers
![](https://i.loli.net/2021/06/27/oGJHNA7MflXiUrg.png)

- 重写`WebMvcConfigurationSupport`中的`addResourceHandlers`方法，添加对静态资源目录的过滤

![](https://i.loli.net/2021/06/27/8bGfQw9j4SCmMXz.png)
- 当然，还需要正确的目录结构

![](https://i.loli.net/2021/06/27/fI2VMtE5lRgUdxq.png)
- 拦截器完整代码：

```java
package com.zby.config;

import com.zby.interceptor.JwtInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

//配置拦截器
@Component
@Configuration
public class JwtConfiguration extends WebMvcConfigurationSupport {

    @Autowired
    private JwtInterceptor jwtInterceptor;

    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        /**
         * addInterceptor ：添加拦截方法
         * addPathPatterns ：添加拦截请求路径（/** ：拦截一切请求）
         * excludePathPatterns ：加入白名单（此请求不拦截）
         * */
        //加载jwtInterceptor拦截规则
        registry.addInterceptor(jwtInterceptor).addPathPatterns("/**")
                .excludePathPatterns("/user/login/**","/arsenal/view","/user/index/**");//因为login之后才会生成token
    }


    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**")
                .addResourceLocations("classpath:/static/");
        registry.addResourceHandler("/templates/**")
                .addResourceLocations("classpath:/templates/");
        super.addResourceHandlers(registry);
    }

    /**
     * 以下WebMvcConfigurerAdapter 比较常用的重写接口
      */
//    /** 解决跨域问题 **/
//    public void addCorsMappings(CorsRegistry registry) ;
//    /** 添加拦截器 **/
//    void addInterceptors(InterceptorRegistry registry);
//    /** 这里配置视图解析器 **/
//    /** 视图跳转控制器 **/
//    void addViewControllers(ViewControllerRegistry registry);
//    void configureViewResolvers(ViewResolverRegistry registry);
//    /** 配置内容裁决的一些选项 **/
//    void configureContentNegotiation(ContentNegotiationConfigurer configurer);
//    /** 视图跳转控制器 **/
//    void addViewControllers(ViewControllerRegistry registry);
//    /** 静态资源处理 **/
//    void addResourceHandlers(ResourceHandlerRegistry registry);
//    /** 默认静态资源处理器 **/
//    void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer);

}
```

- 验证
![](https://i.loli.net/2021/06/27/BkJI19fFRaWK3Tu.png)