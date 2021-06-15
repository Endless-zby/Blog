---
title: Docker 安装使用
updated: 2021-05-23 00:24:14
description: Docker
tags:
- Docker
# 置顶优先级 数值越大优先级越高 #
sticky: 9930
# 【可选】文章分类 #
categories: Docker
# 【可选】文章关键字 #
keywords:
- Docker
date: 2019-07-05 14:41:09
---

> docker的安装与启动（从wordPress迁移过来丢失了大量截图）
---
## 环境

> Centos7（内核版本高于3.10）

### 查询版本：
```shell
  uname -r
```


### 更新版本：
```shell
  yum update
```
## 安装
```shell
  yum -y install docker
```

## 启动
```shell
  systemctl start docker
```

## 设置自启
```shell
  systemctl enable docker
```


## docker使用

> Docker 是一个[开源](https://baike.baidu.com/item/%E5%BC%80%E6%BA%90/246339)的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 [Linux](https://baike.baidu.com/item/Linux)或Windows 机器上，也可以实现[虚拟化](https://baike.baidu.com/item/%E8%99%9A%E6%8B%9F%E5%8C%96/547949)。容器是完全使用[沙箱](https://baike.baidu.com/item/%E6%B2%99%E7%AE%B1/393318)机制，相互之间不会有任何接口。

### docker的常用命令：

1. `docker pull` : 从 Docker Hub 中拉取或者更新指定镜像。

2. `docker images` : 列出本地所有镜像。

3.  `docker ps` : 列出所有正在运行的容器
    `-a` : 列出所有容器（含沉睡镜像）

4. `docker rmi` : 从本地移除一个或多个指定的镜像。

5. `docker rm` : -f 强行移除该容器，即使其正在运行；
   `-l` 移除容器间的网络连接，而非容器本身；
   `-v` 移除与容器关联的空间。

6. `docker kill` : 杀死一个或多个指定容器进程。

### 目的

使用docker的原因就是docker中应用快速安装以及配置（少量配置）的特点

### Docker中软件镜像的获取及启动

* Redis

1. 拉取镜像
```shell
docker pull redis 
```

2. 启动
```shell
docker run -di --name=myproject\_myredis -p 6379:6379 redis redis-server --appendonly yes
```

* RabbitMQ

1. 拉取镜像

```shell
docker pull daocloud.io/library/rabbitmq:management
```

2. 启动
```shell
docker run -di --name=myproject\_rabbitmq -p 15672:15672 -p 15671:15671 -p 5672:5672 -p 5671:5671 -p 4369:4369 -p 25672:25672 daocloud.io/library/rabbitmq:management
```

3. 访问地址(默认端口15672)

```http request
http://***.***.***.***\****:15672
```

4. 默认账号密码
```text
guest
```

*  mongoDB

1. 拉取镜像
```shell
docker pull mongo
```

2. 启动
```shell
docker run -di --name=docker\_mongodb -p 27017:27017 -v /dev/data:/data/db daocloud.io/library/mongo
```
3. 测试
MongoDB中存储的文档必须有一个"\_id" 。这个键值可以是任何类型，默认是ObjectID对象。在一个集合里，每个文档都有一个唯一的“\_id”，确保集合里的每个文档都能被唯一标示。

ObjectID使用12字节的存储空间，是一个由24个16进制数字组成的字符串。

ObjectId的12个字节按照如下方式生成

![](http://zby123.club/wp-content/uploads/2019/07/mongodb的_id规则.png)

时间戳：

　　时间戳，前四个字节是从标准纪元开始的时间戳，单位是秒。可提供秒级别的唯一性。

　　由于时间戳在前，这意味着ObjectId大致按照插入的顺序排列。

　　这四个字节也隐含了文档的创建时间。

机器：

　　主机的唯一标识符。通常是机器主机的散列值（hash）。这样可以确保不同的机器生成不同的ObjectId　　

PID：

　　为了确保在同一台机器上并发的多个进程产生的ObjectID是唯一的，接下来者两个字节产生来自于进程的标识符

计数器：

　　最后三个字节是一个自动增加的计数器，确保相同的进程同一秒产生的ObjectId也是不一样的一秒钟最多允许每个进程拥有2563个不同的ObjectId

自定义\_id：  
db.comments.insert( {\_id:"1001",content:"contentxxx", id:"123" });

查询：  
db.comments.find()