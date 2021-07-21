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
> `Settings` -> `Developer settings` -> `Personal access tokens`

> 注意令牌`只显示一次`

![](https://i.loli.net/2021/07/21/RyHA2gIuqiFNcOP.png)

### 设置GitHub webhooks
> webhooks是什么：webhook允许在某些事件发生时通知外部服务。当指定的事件发生时，我们将向您提供的每个url发送POST请求。
> `也就是说` 当某个分支发生push时GitHub会主动通知Jenkins，使Jenkins感知到项目的变动并自动打包

![](https://i.loli.net/2021/07/21/TpFeHdjQacw9Gb1.png)
# 设置Jenkins的GitHub配置

### Jenkins创建任务
![](https://i.loli.net/2021/07/21/h3VPyK94wYsQtZz.png)
### Jenkins 系统设置
- 配置GitHub服务器
![](https://i.loli.net/2021/07/21/ruzwt8kJOZlYsdp.png)
![](https://i.loli.net/2021/07/21/K9JZzSv3og85qaT.png)
  
### Jenkins 项目设置
- General
![](https://i.loli.net/2021/07/21/Tsqi8KmOLPWjGFx.png)
- 源码管理
![](https://i.loli.net/2021/07/21/wOZXHKB2umI9iDJ.png)  
![](https://i.loli.net/2021/07/21/zEviXPNWw4aBm7F.png)
- 构建触发器
![](https://i.loli.net/2021/07/21/Grtw8gMjU75lkA3.png)
![](https://i.loli.net/2021/07/21/UqnXLpriv5tK8Y4.png)
- 构建环境
![](https://i.loli.net/2021/07/21/WlcwrYf85jiAbFh.png)  
![](https://i.loli.net/2021/07/21/sdiHqEaJvxyFWMY.png)  
- 构建前shell
![](https://i.loli.net/2021/07/21/8xKpOj6H9NFrt4o.png)
  
- 构建后执行shell脚本
> 安装插件 Post Build task 并重启

![](https://i.loli.net/2021/07/21/wWyD7zSXk94GLc6.png)
![](https://i.loli.net/2021/07/21/FspVyHYb4a3B61u.png)

### 全局工具配置
- 安装maven
> 在服务器上安装maven并配置 /etc/profile 环境变量

![](https://i.loli.net/2021/07/21/GCzeZuxJ6A3oqYy.png)
![](https://i.loli.net/2021/07/21/8kgiWGwIez9Uacb.png)

# 完成验证
在将本地的项目push下之后看到Jenkins感知到项目的变化已经在轮询拉去最新的代码并打包
![](https://i.loli.net/2021/07/21/1DmsPTJrILSMZY8.png)
![](https://i.loli.net/2021/07/21/6YWIskZvfHqpRew.png)

打包完成

![](https://i.loli.net/2021/07/21/OdlDexBRI9ufbmj.png)

# 发布（一键部署）

> 使用宝塔的一键部署

配置启动脚本
![](https://i.loli.net/2021/07/21/wdUHIgzrsKpLXqo.png)
查看项目列表
![](https://i.loli.net/2021/07/21/RredbHzXByGuapn.png)
看日志启动完成了
![](https://i.loli.net/2021/07/21/c5SbxdwZ8sCRUyP.png)