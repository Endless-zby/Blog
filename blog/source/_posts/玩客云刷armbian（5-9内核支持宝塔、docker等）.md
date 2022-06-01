---
title: 玩客云刷armbian（5.9内核支持宝塔、docker等）
date: 2022-03-06 22:35:04
updated: 2022-03-06 22:35:04
top_img: https://s2.loli.net/2022/03/06/VQXbRJCH9qmcZsO.jpg
cover: https://s2.loli.net/2022/03/06/iyAujYkJcgLQPD2.jpg
description: 取自恩山论坛【mysoy】大佬编译armbian 20.19 5.9.0镜像，该镜像内核版本较高可以完美支持docker，以docker安装openwrt为例
tags:
- Linux
- armbian  
- openwrt
# 置顶优先级 数值越大优先级越高 #
sticky: 100016
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- Linux
- openwrt
---

> 我使用的是一个三代玩客云主机，主板版本是v1.2，刷的snail底包，这里就不细说如何刷入底包和armbian了，网上有很多的文字、视频教程，这里就只提几点注意事项，并附带所有的刷机镜像和工具

## 工具和镜像下载地址
{% btn 'https://www.aliyundrive.com/s/Bs5FUDsnXGQ',阿里云网盘下载,far fa-hand-point-right,orange larger %}


{% btn 'https://cloud.189.cn/t/RZ7jyiUvUz6r',天翼云盘下载,far fa-hand-point-right,green larger %}
{% note info no-icon %}
天翼云盘访问码：`rlc9`
{% endnote %}

### 工具介绍

软件/镜像 | 说明 | 注意
---|---|----
balenaEtcher-Portable-1.5.45| u盘镜像制作工具 |  
inphic-S805-支持硬解| 电视盒子系统（直刷） |  可以刷着玩玩
s805_flash_snail_2| 安卓底包，支持从usb启动 |  底包再成功刷入之后，如需再次刷入就可以不用拆机短接了，直接按住reset再通电即可
USB_Burning_Tool_v2.1.3| 晶晨固件的烧录工具 |  
WKY-Armbian_20.12_5.9.0| Armbian系统 |
virking/openwrt:onecloud| openwrt镜像 |  此镜像中包含ShadowSocksR Plus+服务


### 注意点

- Armbian镜像`账号`:root  `密码`: 1234
- openwrt镜像`账号`:root  `密码`: password
- Armbian写入EMMC
```shell
cd /boot/install
./install.sh
```

## 实例

{% btn 'https://byzhao.cn/2022/02/13/openWRT%E6%97%81%E8%B7%AF%E7%94%B1/',openwrt部署可以参考此处,far fa-hand-point-right,blue larger %}


{% btn 'https://www.right.com.cn/forum/thread-4112583-1-1.html',其他环境搭建,far fa-hand-point-right,blue larger %}

