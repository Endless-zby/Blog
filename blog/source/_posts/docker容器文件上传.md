---
title: docker容器文件上传
updated: 2021-05-23 00:24:14
description: Docker
tags:
- Docker
# 置顶优先级 数值越大优先级越高 #
sticky: 9931
# 【可选】文章分类 #
categories: Docker
# 【可选】文章关键字 #
keywords:
- Docker
date: 2019-04-19 13:28:28
---

### 上传命令
```shell
docker cp /ik myproject_myelasticsearch:/usr/share/elasticsearch/plugins
```
### 下载命令
```shell
docker cp myproject_myelasticsearch:/usr/share/elasticsearch/plugins /ik
```

> myproject\_myelasticsearch 是应用名

### 进入应用镜像

```shell
docker exec -it myproject_myelasticsearch /bin/bash
```