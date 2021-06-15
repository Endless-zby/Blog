---
title: SpringMVC过滤swagger的拦截
updated: 2021-06-13 00:24:14
description: SpringMVC过滤swagger的拦截
tags:
- Java
# 置顶优先级 数值越大优先级越高 #
sticky: 9902
# 【可选】文章分类 #
categories: Java
# 【可选】文章关键字 #
keywords:
- Java
- SpringBoot
date: 2020-04-02 11:09:06
---

> 增加拦截器后发现swagger无法访问，是因为swagger的内置接口被拦截了

- 继承WebMvcConfigurationSupport类后重写addResourceHandlers方法

- 重写 addResourceHandlers 方法如下：

![](https://www.zby123.club/wp-content/uploads/2020/04/swagger-1024x297.png)

```java
@Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**")
                .addResourceLocations("classpath:/static/");
        registry.addResourceHandler("/templates/**")
                .addResourceLocations("classpath:/templates/");
        registry.addResourceHandler("swagger-ui.html").addResourceLocations(
                "classpath:/META-INF/resources/");
        registry.addResourceHandler("/webjars/**").addResourceLocations(
                "classpath:/META-INF/resources/webjars/");
    }
```