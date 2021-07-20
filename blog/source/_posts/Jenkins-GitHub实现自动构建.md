---
title: Jenkins+GitHub实现自动构建
date: 2021-07-19 23:36:04
updated: 2021-07-19 21:30:21
top_img: https://i.loli.net/2021/07/20/Tl3Nb1G6E4Lihe8.jpg
cover: https://i.loli.net/2021/07/20/q12YobNredQxuaL.jpg
description: 使GitHub的每次提交都能主动通知到jenkins，jenkins自动拉取最新代码进行打包配合脚本实现自动发布
tags:
- 工具
# 置顶优先级 数值越大优先级越高 #
sticky: 100002
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- jenkins
- github
- 持续集成
---

# 安装环境
- 公网ip
- JDK  
- 合适的Jenkins安装包


# 安装jdk
> 先省略掉这步

# 准备Jenkins环境
### 下载
下载地址：https://mirrors.tuna.tsinghua.edu.cn/jenkins/redhat/

### 安装

```shell
rpm -ivh jenkins-2.302-1.1.noarch.rpm
```

### 配置
> 修改Jenkins的java`依赖环境`

```shell
vim /etc/rc.d/init.d/jenkins
```
![](https://i.loli.net/2021/07/20/CVYqAykRPa8QKJS.png)

> 配置jenkins运行参数
```shell
vim /etc/sysconfig/jenkins
```
![](https://i.loli.net/2021/07/20/GcFZLyi4VsMhAxm.png)


### 启动/停止/重启/状态
```shell
service jenkins start/stop/restart/status
```
![](https://i.loli.net/2021/07/20/ZjqgJh5L8FifrRt.png)

```shell
[root@localhost ~]# service jenkins start
Starting jenkins (via systemctl):  Warning: jenkins.service changed on disk. Run 'systemctl daemon-reload' to reload units.
                                                           [  确定  ]
```
> 启动成功但是有警告，按照他说的执行下`systemctl daemon-reload` 再重启Jenkins即可

### 登录Jenkins

![](https://i.loli.net/2021/07/20/yzEMFmAV6RHJUZn.png)

查看初始密码：
```shell
cat /var/lib/jenkins/secrets/initialAdminPassword
```
![](https://i.loli.net/2021/07/20/hfG2yHCOFm45bwP.png)

### 安装插件
> 使用推荐插件即可

![](https://i.loli.net/2021/07/20/qteRZwjmPyTNVGz.png)


# 配置GitHub相关内容

### 获取GitHub令牌
> 注意只显示一次

### 设置GitHub webhooks


# 设置Jenkins的GitHub配置

### Jenkins创建任务

### 系统设置中添加GitHub服务器

### 