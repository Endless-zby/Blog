---
title: Apache · ab
updated: 2021-05-23 00:24:14
description: ab是apache自带的压力测试工具。ab非常实用，它不仅可以对apache服务器进行网站访问压力测试，也可以对或其它类型的服务器进行压力测
tags:
- Java
# 置顶优先级 数值越大优先级越高 #
sticky: 9960
# 【可选】文章分类 #
categories: Java
# 【可选】文章关键字 #
keywords:
- ab
date: 2020-07-06 18:55:33
---

> ab是apache自带的压力测试工具。ab非常实用，它不仅可以对apache服务器进行网站访问压力测试，也可以对或其它类型的服务器进行压力测

*   \-n即requests，用于指定压力测试总共的执行次数。

*   \-c即concurrency，用于指定压力测试的并发数。

*   \-t即timelimit，等待响应的最大时间(单位：秒)。

*   \-b即windowsize，TCP发送/接收的缓冲大小(单位：字节)。

*   \-p即postfile，发送POST请求时需要上传的文件，此外还必须设置`-T`参数。

*   \-u即putfile，发送PUT请求时需要上传的文件，此外还必须设置`-T`参数。

*   \-T即content-type，用于设置Content-Type请求头信息，例如：`application/x-www-form-urlencoded`，默认值为`text/plain`。

*   \-v即verbosity，指定打印帮助信息的冗余级别。-w以HTML表格形式打印结果。

*   \-i使用HEAD请求代替GET请求。

*   \-x插入字符串作为table标签的属性。-y插入字符串作为tr标签的属性。

*   \-z插入字符串作为td标签的属性。

*   \-C添加cookie信息，例如："Apache=1234"(可以重复该参数选项以添加多个)。

*   \-H添加任意的请求头，例如："Accept-Encoding: gzip"，请求头将会添加在现有的多个请求头之后(可以重复该参数选项以添加多个)。

*   \-A添加一个基本的网络认证信息，用户名和密码之间用英文冒号隔开。

*   \-P添加一个基本的代理认证信息，用户名和密码之间用英文冒号隔开。

*   \-X指定使用的代理服务器和端口号，例如:"126.10.10.3:88"。

*   \-V打印版本号并退出。

*   \-k使用HTTP的KeepAlive特性。

*   \-d不显示百分比。

*   \-S不显示预估和警告信息。

*   \-g输出结果信息到gnuplot格式的文件中。

*   \-e输出结果信息到CSV格式的文件中。

*   \-r指定接收到错误信息时不退出程序。

*   \-h显示用法信息，其实就是`ab -help`。

简单示例：

![](https://www.zby123.club/wp-content/uploads/2020/07/ab-demo.png)

Result：

![](https://www.zby123.club/wp-content/uploads/2020/07/ab-help.png)