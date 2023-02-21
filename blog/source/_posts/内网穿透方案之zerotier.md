---
layout: posts
title: 内网穿透方案之zerotier
date: 2023-02-14 14:31:13
updated: 2023-02-14 14:31:13
top_img: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023214-151715-jv2fjt3hc4zerotier2.jpg
cover: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023214-152917-92plwhg72szerotier3.jpeg
description: 相对于白嫖用户的Frp来说zerotier可以充分利用家庭宽带的上行带宽，可以最大限度的满足nas用户的外网访问需求。
tags:
- Linux
- 工具
# 置顶优先级 数值越大优先级越高 #
sticky: 100065
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- zerotier
- 内网穿透
---

## 需求场景

> 对于需要随时随地的访问家里网络设备（例如：nas、服务器、软路由等）的用户来说，能在运营商搞到免费的固定ipv4或者动态ipv4是一件非常重要的事情，但是既然你看到这篇文章了，那就代表着你没能成功  
> 这种情况下有两种解决方案，一是`端口流量转发`，例如：Frp、花生壳等，二是`虚拟局域网`，例如：zerotier、Tailscale   
 
> 大概说下我自己的网络状态，北京移动，有动态ipv6，每月4日，14日，24日ip地址会发生变化，平时基本不会变，设备有：  

- nas：做数据存储照片和jellyfin看电影  
- nuc：一台巴掌大的nuc当服务器用，N4100 + 8G + 256G，跑着一堆开发工具，还有ddns对ipv6地址做域名解析  
- 红米AC2100：已经被我刷了padavan老毛子的固件，做软路由  
- 玩客云：刷了电视盒子

每个应用通过ddns和nginx的配置都有自己的域名，看似很美好，但这一切都依赖于ipv6，`BUT`公司网络不支持ipv6，这就直接导致我在公司时与家里的设备彻底失去了联系，但是zerotier可以将我分布在不同地域的不同客户端组成一个局域网，在局域网将不受限的互相访问，简单的场景就是在公司打开nas下载一部电影等着我下班回去边吃边放松

## 了解zerotier
- 
- 
- 

## 如何使用




## 扩展插件（uTools）

> 为了方便使用，我在uTools为zerotier开发了一款插件，可以作为zerotier的客户端来使用，更加方便的查看局域网内各节点的状态，或者移除节点，修改节点信息，使用方法：uTools插件应用市场中搜索`zerotier`点击获取即可

![utools插件市场搜索](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023214-153805-ot9jiehv5kzerotier4.jpg)

![zerotier插件介绍](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023214-153805-ocddjmu461zerotier5.jpg)

![zerotier插件使用界面](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023214-153805-7wc10h2w7xzerotier6.jpg)

插件将有计划的持续升级，如果有更好的想法欢迎在评论区留言
