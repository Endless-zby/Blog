---
title: OpenWRT旁路由
date: 2022-02-13 22:52:16
updated: 2022-02-13 22:52:16
top_img: https://s2.loli.net/2022/02/13/DQfL83uosmYq6F4.jpg
cover: https://s2.loli.net/2022/02/13/DQfL83uosmYq6F4.jpg
description: 旁路由本身相对于路由器拥有性能强大的处理器和大内存，在作为网关的时候可以为我们提供安全可靠的网络环境，OpenWrt作为一款强大的嵌入式系统，拥有丰富的插件，例如【广告屏蔽大师Plus+】、【ShadowSocksR Plus+】、【网络唤醒】等，使我们多设备网络非常方便
tags:
- Linux
- openwrt
# 置顶优先级 数值越大优先级越高 #
sticky: 100011
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- Linux
- openwrt
---


![openwrt旁路由网络拓扑示意图.png](https://s2.loli.net/2022/02/15/HygRpx87okWzaSP.png)

## 安装openwrt
- 找到网卡
```shell
ifconfig
```
- 打开网卡混杂模式
> `eno1`是所选网卡,因为我们需要让旁路由作为网关使用，就需要打开网卡的混杂模式，让它能接收到所有经过它的数据，而不论其目的地址是否是它
```shell
ip link set eno1 promisc on
```  
- 创建docker局域网络
> 说明：`macvlan`是我们创建的网络名  ，`gateway`设置为主路由ip，也就是网关地址
```shell
docker network create -d macvlan --subnet 192.168.0.0/24 --gateway=192.168.0.1 -o parent=eno1 macnet
```
- 查看docker局域网络
```shell
docker network ls
```
![这是我们刚创建的网络macvlan](https://s2.loli.net/2022/02/13/kxdpVPovUIaNHcq.png)
- pull openwrt x86架构的镜像
```
docker pull hou6807628/openwrt:x86_generic
```

- 创建镜像启动
> `--restart always` 设置为docker自动重启
```shell
docker run  --restart always --name openwrt -d --network macnet --privileged hou6807628/openwrt:x86_generic /sbin/init
```
- 进入容器
```shell
docker exec -it openwrt bash
```
- 打开`/etc/config/network`
![](https://s2.loli.net/2022/02/13/yx3vPARTg8fbh1c.png)
- 修改IP地址
> 设置一个旁路由工作的ip，这个ip会作为整个局域网的网关使用

![](https://s2.loli.net/2022/02/13/ED4ubRWJdTr3Qzw.png)
- 保存退出并重启网络
```shell
/etc/init.d/network restart
```

- 退出容器
```shell
exit
```

## 访问openwrt

> 默认账号密码：root / password

![](https://s2.loli.net/2022/02/13/y2ChUY6L8zJwXTd.png)

## 配置openwrt
> 我们使用旁路由的目的是利用其更好的硬件设备提高网络质量，并且将它将作为我们整个网络的网关的话，可以使用到openwrt中各种优秀的插件

- 网络配置
> 【网络】 -> 【接口】

![](https://s2.loli.net/2022/02/13/kUyxpbMXqEAvQdT.png)
![](https://s2.loli.net/2022/02/13/a5GysUCNAFWdRKB.png)
![](https://s2.loli.net/2022/02/13/1Sx2Gfp3Z4K5mDq.png)
![](https://s2.loli.net/2022/02/13/dphPLYF2rvDwgs3.png)


## 配置主路由
> 将主路由的网关设置为旁路由的ip
> 我使用的主路由是TL-WDR5660型号的路由器，配置很简单，参考我的配置，针对自己路由器找到对应的配置项配置就行了

![](https://s2.loli.net/2022/02/13/ZYL7sIjKkz3pJMD.png)

## 尾巴
> 在这我们可以监控整个网络使用情况，当然作为旁路由它的主要目标可不是为了看系统数据报表，而是为了一揽子的实用插件

![](https://s2.loli.net/2022/02/13/1bJAKUvMWI7kdq2.png)

> 这个镜像为我们提供了以下插件：

![](https://s2.loli.net/2022/02/13/7QpZ91OyKcABT36.png)
![](https://s2.loli.net/2022/02/14/1UawnTfODeXqbdR.png)

我们先大概了解几个
- ShadowSocksR Plus+
> 这个软件有多么强大懂的都懂，当它被放置到网关的时候是不是就意味着整个局域网设备都具备了科学上网的能力了？并且支持 SS/SSR/V2RAY/TROJAN/NAIVEPROXY/SOCKS5/TUN 等协议，以及自动订阅并切换节点，非常省心

- 广告屏蔽大师 Plus+
> 广告屏蔽大师 Plus + 可以全面过滤各种横幅、弹窗、视频广告，同时阻止跟踪、隐私窃取及各种恶意网站

- ServerChan
> 是一款从服务器推送报警信息和日志到微信的工具。 开源地址：https://github.com/tty228/luci-app-serverchan

- Apple AirPlay
> Apple AirPlay 2 是一个强大易用的无损音频服务器( iOS/MacOSX 原生支持)
