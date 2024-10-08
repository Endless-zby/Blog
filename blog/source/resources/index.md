---
title: resources
date: 2022-06-11 12:40:00
updated: 2022-06-11 12:40:00
description: "福利"
top_img: 
---

## 2024-10-08
#### JetBrains全家桶激活方法
```text
idea激活码,pycharm激活码,goland激活码
```
{% btn 'https://pan.baidu.com/s/1AwmvwkvJ3INOn_1vPnN5BQ?pwd=1234',下载激活工具,far fa-hand-point-right,green larger %}
运行要激活的工具对应的vbs即可
![激活码](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2024108-r4wcf5envl1728355125656.png)



## 2024-09-23
#### 还可以正常使用的镜像源（经测试可用）

```shell
# 不存在就新建
vim /etc/docker/daemon.json
```
```json
{
    "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://dockerhub.icu",
        "https://docker.anyhub.us.kg",
        "https://docker.1panel.live"
    ]
}
```
```shell
# 重启守护进程
sudo systemctl daemon-reload  #重启daemon进程
sudo systemctl restart docker  #重启docker
```
---------

## 2024-08-05
#### ~~可以正常使用的镜像源（经测试可用）~~
```json
{
    "registry-mirrors": [
        "https://registry.docker-cn.com", 
        "http://hub-mirror.c.163.com"
    ]
}
```

---------