---
title: LomBok
updated: 2021-05-23 00:24:14
description: LomBok
tags:
- 工具
# 置顶优先级 数值越大优先级越高 #
sticky: 9970
# 【可选】文章分类 #
categories: 工具
cover: https://i.loli.net/2021/06/19/A3mKTx4UnHlq9J2.jpg
# 【可选】文章关键字 #
keywords:
- LomBok
date: 2020-04-24 22:03:16
---

添加依赖：

```xml
<dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
</dependency>
```

安装插件：

![](https://i.loli.net/2021/06/19/8n4IcPqYTBtvKC1.png)

基本注解：

```text
@Data：作用于类上，是以下注解的集合：
{
     @ToString
     @EqualsAndHashCode: 作用于类，覆盖默认的equals和hashCode
     @Getter
     @Setter
     @RequiredArgsConstructor: 生成包含final和@NonNull注解的成员变量的构造器
}
```

``` text
`@NonNull`：主要作用于成员变量和参数中，标识不能为空，否则抛出空指针异常
`@Log`：作用于类上，生成日志变量
`@AllArgsConstructor`: 全参构造器
`@NoArgsConstructor`: 无参构造
`@Builder`: 作用于类上，将类转变为建造者模式
```

示例：

![](https://i.loli.net/2021/06/19/WIb4DEi2y6aB9nH.png)

使用LomBok注解实体类

![](https://i.loli.net/2021/06/19/3lmC4rK17H86vxz.png)

可以看到，LomBok自动配置出了所有属性的get、set方法还可以使用建造者模式