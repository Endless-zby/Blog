---
title: Spring boot 跨域请求问题（403）
updated: 2021-06-13 00:24:14
description: Spring boot 跨域请求问题（403）
tags:
- Java
# 置顶优先级 数值越大优先级越高 #
sticky: 9903
# 【可选】文章分类 #
categories: Java
# 【可选】文章关键字 #
keywords:
- Java
- SpringBoot
date: 2019-05-14 12:59:33
---

## 解决方案：

```java
/**
 * 设置跨域请求
 */
@Configuration
public class MyConfiguration {
    @Bean
    public FilterRegistrationBean corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowCredentials(true);
        config.addAllowedOrigin("*");// 1 设置访问源地址
        config.addAllowedHeader("*");// 2 设置访问源请求头
        config.addAllowedMethod("*");// 3 设置访问源请求方法
        source.registerCorsConfiguration("/**", config);
        FilterRegistrationBean bean = new FilterRegistrationBean(new CorsFilter(source));
        bean.setOrder(0);
        return bean;
    }
}
```