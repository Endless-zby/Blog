---
title: itx小主机建站
date: 2021-07-15 22:16:09
updated: 2021-07-15 22:16:09
top_img: https://i.loli.net/2021/07/20/6HTZhODgsoQ4Pcj.png
cover: https://i.loli.net/2021/07/20/6HTZhODgsoQ4Pcj.png
description: 有一台自己的服务器可以做哪些很酷的事情？ NAS？ 博客？ 爬虫？ 私有云？......
tags:
- Linux
# 置顶优先级 数值越大优先级越高 #
sticky: 100000
# 【可选】文章分类 #
categories: Linux
# 【可选】文章关键字 #
keywords:
- Linux
- 服务器
---

# 安装系统
> 安装 `CentOS 7` 最小包 
> http://mirrors.aliyun.com/centos/7/isos/x86_64/   阿里云站点
# 系统更新
```shell
yum update
```
# 控制台
> 安装宝塔控制面板（省时省心）
> https://www.bt.cn/

{% btn 'https://www.bt.cn/',宝塔控制面板官网,far fa-hand-point-right,green larger %}

```shell
curl -sSO http://download.bt.cn/install/install_panel.sh && bash install_panel.sh
```

# 安装小工具

## lrzsz
> lrzsz是一款在linux里可代替ftp上传和下载的程序

```shell
yum -y install lrzsz
```
---
## 更新wget

> 所有版本：http://mirrors.ustc.edu.cn/gnu/wget/

{% btn 'http://mirrors.ustc.edu.cn/gnu/wget/',查看所有版本,far fa-hand-point-right,orange larger %}

- 下载当前最新版本的wget包
```shell
wget http://mirrors.ustc.edu.cn/gnu/wget-1.19.4.tar.gz
```

- 卸载自带wget
```shell
yum remove -y wget
```

- 安装依赖
```shell
yum install -y openssl-devel
```

- 解压
```shell
tar zxvf wget-1.19.4.tar.gz
cd wget-1.19.4
```

- 编译安装
```shell
./configure --prefix=/usr --sysconfdir=/etc --with-ssl=openssl

make && make install
```

- 查看版本
```shell
wget -V
```
---
# 动态域名解析



