---
title: Java实体映射工具-MapStruct 的坑
updated: 2021-06-13 00:24:14
description: Java实体映射工具-MapStruct 的坑
tags:
- Java 
cover: https://i.loli.net/2021/06/25/fFpQi2wJKyVLduS.jpg
# 置顶优先级 数值越大优先级越高 #
sticky: 9969
# 【可选】文章分类 #
categories: Java
# 【可选】文章关键字 #
keywords:
- MapStruct
date: 2020-09-14 18:50:58
---

{% btn 'http://note.youdao.com/noteshare?id=79062551314a8772ffd6b6b0720ada8b',了解MapStruct,far fa-hand-point-right,green larger %}

> 经过初步的MapStruct试用，大概总结一下，目前的MapStruct版本（1.4.0.CR1）最佳的试用场景还是对于简单对象的映射比较方便，对于涉及到业务逻辑代码的部分并不是很推荐使用，等待后期的完善。


> `坑`：还原一个在实际中遇到的问题（以下是写的简单示例）

### 假设有三个实体，他们之间的关系如下：

![](https://i.loli.net/2021/06/19/RPqWIJ8MQsod9xe.png)

- Person

![](https://i.loli.net/2021/06/19/u1iMpa3O5rQsFv4.png)

- User

![](https://i.loli.net/2021/06/19/2wNXIHbZpB6Rmkq.png)

- UserInfo

### 映射关系如下

![](https://i.loli.net/2021/06/19/xsrVI1JtNRMPThi.png)
- Mapping

> 目前的这种情况下编译器可以成功编译

__`但是`__

![](https://i.loli.net/2021/06/19/CUjJeSox9gfQRdV.png)

### 异常Mapping

> 当我们只想建立入参user中的name属性和Person中的user的name关系的时候就会报错

```java
Error:(23, 12) java: Several possible source properties for target property "name".
```

> 直译就是目标属性“name”可能有`多个源属性`，也就是说此时编译的时候虚拟机找不到source中的name对应Person中的哪一个name（Person中存在name属性，Person中的User也存在该属性）

### 下面设置多个实验组对照

![](https://i.loli.net/2021/06/19/CUjJeSox9gfQRdV.png)

![](https://i.loli.net/2021/06/19/SsNZl6wuD5bAMxV.png)

![](https://i.loli.net/2021/06/19/xsrVI1JtNRMPThi.png)

> 可以看到实体中存在相同属性名的时候优先最外层的属性获取映射关系，如果要在这种情况下只对Person中User的name设置映射关系，就需要对Person中的name忽略掉

> 如图：

![](https://i.loli.net/2021/06/19/8hzrfpZg3k6nUWt.png)