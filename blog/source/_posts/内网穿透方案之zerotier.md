---
layout: posts
title: 内网穿透方案之Zerotier
date: 2023-02-14 14:31:13
updated: 2023-02-14 14:31:13
top_img: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023214-151715-jv2fjt3hc4zerotier2.jpg
cover: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023214-152917-92plwhg72szerotier3.jpeg
description: 相对于白嫖用户的Frp来说Zerotier可以充分利用家庭宽带的上行带宽，可以最大限度的满足nas用户的外网访问需求。
tags:
- Linux
- 工具
# 置顶优先级 数值越大优先级越高 #
sticky: 100065
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- Zerotier
- 内网穿透
---

## 需求场景

> 对于需要随时随地的访问家里网络设备（例如：nas、服务器、软路由等）的用户来说，能在运营商搞到免费的固定ipv4或者动态ipv4是一件非常重要的事情，但是既然你看到这篇文章了，那就代表着你没能成功  
> 这种情况下有两种解决方案，一是`端口流量转发`，例如：Frp、花生壳等，二是`虚拟局域网`，例如：Zerotier、Tailscale   
 
> 大概说下我自己的网络状态，北京移动，有动态ipv6，每月4日，14日，24日ip地址会发生变化，平时基本不会变，设备有：  

- nas：做数据存储照片和jellyfin看电影  
- nuc：一台巴掌大的nuc当服务器用，N4100 + 8G + 256G，跑着一堆开发工具，还有ddns对ipv6地址做域名解析  
- 红米AC2100：已经被我刷了padavan老毛子的固件，做软路由  
- 玩客云：刷了电视盒子

__每个应用通过ddns和nginx的配置都有自己的域名，看似很美好，但这一切都依赖于ipv6，`BUT`公司网络不支持ipv6，这就直接导致我在公司时与家里的设备彻底失去了联系，但是Zerotier可以将我分布在不同地域的不同客户端组成一个局域网，在局域网将不受限的互相访问，简单的场景就是在公司打开nas下载一部电影等着我下班回去边吃边放松__

## 了解Zerotier
- Zerotier是强大的内网穿透工具，把不同地域不同网络下的网络设备连接在一起，组成局域网。基于P2P连接，P2P无法连接时Zerotier也将提供中继服务
- 丰富的客户端和插件，Zerotier提供了Android、IOS、mac、windows、linux，群晖套件等等客户端，使得组网的设备拥有极强的扩展性，想象一下用自己的手机可以随时随地的访问公司的电脑、家里的电脑、nas

## 如何使用
- 注册账号  
{% btn 'https://www.zerotier.com/',Zerotier官网,far fa-hand-point-right,orange larger %}
![注册账号](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023317-zl9tebj1y51679066562623.png)
  
- 创建网络
![创建网络](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023317-zx5rshqb6d58cc70ffbaae9e27b153aefdc1c52bf.png)
创建一个网络
![创建网络](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023317-hmf8z5h7jkca75f8b24af28f942266d6b47cdc2b4.png)  
基本设置
![创建网络](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023317-vhbghe3cksde750bc5579a9532eca3a9f5316ba8e.png)
选择一个合适的虚拟网段

- 下载客户端并加入网络
![下载客户端](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023317-6i5ruge18l35a0743388da40b30e1c5f9f7e8ddd9.png)

![下载客户端](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023317-69hy3sgdl33605f0565e60205ba47ecefb274cd38.png)
以windows为例点击【__join new network__】输入创建的网络id，等待一小会即可在后台看到已经加入的设备，在对应的设备前面打上对勾√，表示已经授权加入网络
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023317-ipza0s97zj2393873851631e0299cee963af4bf13.png)
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023317-sojvk0y161f9ef4afebccee829ef9088ed0664ba1.png)

- 测试
现在就可以方便的访问各个设备了
例如：在公司访问家中的linux主机
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023317-ttv25mh6mbc6aba066f82505354bc7e0c39e181f5.png)

## 扩展插件（uTools）

> 为了方便使用，我在uTools为Zerotier开发了一款插件，可以作为Zerotier的客户端来使用，更加方便的查看局域网内各节点的状态，或者移除节点，修改节点信息，使用方法：uTools插件应用市场中搜索`Zerotier`点击获取即可

![utools插件市场搜索](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023214-153805-ot9jiehv5kzerotier4.jpg)

![Zerotier插件介绍](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023214-153805-ocddjmu461zerotier5.jpg)

![Zerotier插件使用界面](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023214-153805-7wc10h2w7xzerotier6.jpg)

![Zerotier插件配置界面](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023317-w4sq0r881r1679065409285.png)

插件将有计划的持续升级，如果有更好的想法欢迎在评论区留言
