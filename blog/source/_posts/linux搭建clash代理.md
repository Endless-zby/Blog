---
layout: posts
title: linux搭建clash代理
date: 2023-06-03 11:55:08
updated: 2023-06-03 11:55:08
top_img: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023521-1kfgdj7x9bd665ba46aabf86815143bd5990640ca0-2.jpeg
cover: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023521-1kfgdj7x9bd665ba46aabf86815143bd5990640ca0-2.jpeg
description: 
tags:
- Linux
- 工具
# 置顶优先级 数值越大优先级越高 #
sticky: 100099
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- clash
- 科学上网
- 玩客云
---

## 问题

> 如何让家庭网络中的所有连接设备都可以科学上网，每台设备上都安装小猫咪很麻烦，而且大多数的机场是有客户端数量限制的，如果我们使用一台机器做代理会不会很好的解决这些问题

## 准备

- 一台linux机器（我有一台玩客云在作为nas使用，刚好用上）
- clash 配置文件

## 步骤

### 准备clash config.yaml
从windows的clash中导出一份配置文件留着一会用
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-5ewne7pqxo68f99e004684576b6058ec534a23a07c.jpg)

### 创建config.yaml

- 创建config.yaml配置文件
```shell
mkdir -p /root/clash

vim config.yaml
```
- 配置如下
```yaml
port: 7890
socks-port: 7891
log-level: info
external-controller: '0.0.0.0:9090'
external-ui: /ui
```
- 将从windows导出的yaml配置文件和自己创建的配置做合并
  ![合并之后的文件结构](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-j51f31w5urWechatIMG4875.png)
### 拉取clash镜像并启动

```shell
docker run -d --name clash-client --restart always -p 7890:7890 -p 7891:7891 -p 9090:9090 -v /root/clash/config.yaml:/root/.config/clash/config.yaml -v /root/clash/ui:/ui dreamacro/clash
```
> 如果需要修改端口的话记得把yaml中的配置同步修改了  

![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-kbmh1xemojWechatIMG4868.png)

启动后会在/root/clash中创建ui文件

![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-lsjfc60baiWechatIMG4869.png)

### 配置clash ui

#### 下载ui
```shell
curl https://github.com/haishanh/yacd/tree/gh-pages
```

#### 解压到ui目录中
```shell
unzip yacd-gh-pages.zip -d ui/
```
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-qf87gsbs5iWechatIMG4870.png)

### 重启dreamacro/clash
```shell
docker restart acaa7d30efbd
```

### 访问clash ui页面

> http://192.168.123.208:9090/ui

- clash控制台看板
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-v3syz20kmcWechatIMG4871.png)

- clash节点和策略
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-4jgwh5wzahWechatIMG4874.png)
- clash配置
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-63w1f5zdvnWechatIMG4873.png)
- clash实时连接状态
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-zb2pelsog1WechatIMG4872.png)





### 浏览器插件
#### 谷歌浏览器推荐插件Proxy SwitchyOmega
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-9q6mlle2szWechatIMG4867.jpeg)

#### 设置谷歌浏览器代理
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-8508w3e24sWechatIMG4865.jpeg)

### 系统代理
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-r8mujdbihdf50325e170ec167df9fb5262a4fd581d.jpg)

### 演示
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202363-um342z6opsWechatIMG4866.jpeg)
