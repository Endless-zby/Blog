---
layout: posts
title: CasaOS - Your Home Cloud OS
date: 2023-03-18 00:03:51
updated: 2023-03-18 00:03:51
top_img: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-g0i3hire9c4f3800c0e0268376dce8e80bcb74d92.png
cover: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-grs4nz6ba41679069368744.png
description: 一个简单、美观、优雅的轻量级家庭云系统
tags:
- Linux
- 工具
# 置顶优先级 数值越大优先级越高 #
sticky: 100079
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- nas
- 私有云
- 玩客云
---

### 简介
> Project CasaOS最初是一个基于社区的开源项目，专注于围绕Docker生态系统提供简单的家庭云体验。  
> CasaOS旨在通过数据民主化重新定义创作者和开发者的私有云数字体验，并使每个人都能将这一目标提升到一个新的水平。  
> 如果你正在寻找一个免费的开源家庭云系统，那么CasaOS是你最好的选择。只需将其安装在备用电脑或家庭服务器上，即可将其用作媒体中心。  
> 任何带有web UI和Docker镜像的应用程序都可以安装在上面。  

### 资料
{% tabs windows %}
<!-- tab GitHub @fab fa-github -->

{% btn 'https://github.com/IceWhaleTech/CasaOS',官方github地址,far fa-hand-point-right,green larger %}
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-b6z7hkg7se1679070458613.png)

<!-- endtab -->

<!-- tab 官网 @fa-sharp fa-solid fa-house -->

{% btn 'https://casaos.io/', CasaOS官网,far fa-hand-point-right,blue larger %}
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-oqyl948vig1679070504271.png)
<!-- endtab -->
{% endtabs %}


### 特点
- __为家庭场景设计的友好UI__  
    没有代码，没有形式，直观，人性化设计
- __多种硬件和基本系统支持__
    ZimaBoard，NUC，RPi，旧电脑，任何可用的东西。
- __丰富的应用__
    Nextcloud、Nginx、HomeAssiant、AdGuard、Jellyfin等！轻松安装众多Docker应用程序，Docker生态系统中超过100000个应用程序可以轻松安装
- __优雅的驱动器和文件管理__
    所见即所得，没有任何专业技术要求
- __丰富的小组件__
    资源使用情况、应用状态一目了然
- __良好的硬件兼容、系统兼容__
    Debian 11、Ubuntu Server 20.04、Raspberry Pi OS | Elementary 6.1、Armbian 22.04
### 安装

__只需要一条命令，就可以拥有私有云__
```shell
curl -fsSL https://get.casaos.io | sudo bash
```
或者
```shell
wget -qO- https://get.casaos.io | sudo bash
```
![安装](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-3upzc6pfli6eeb26306b8b81b396d8fd5c5f778c5.png)

- 清爽的UI
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-cc2t3flhok1679071144109.png)

- 拥有丰富的应用
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-39ey9eahml1679071215280.png)
  
- 极高的扩展能力
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-rk1ak1h5iq1679071289082.png)

### 共享资源
在局域网内共享CasaOS上挂载的硬盘
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-jsjrvm9wea1679071679106.png)
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-td6vfbrn4h1679071716069.png)
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-8hbzfzpysq1679071833013.png)
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023318-dr2u0enget1679071875470.png)
### 卸载
一键卸载
```shell
casaos-uninstall
```

