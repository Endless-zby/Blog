---
title: 使用github做免费图床
updated: 2021-05-23 00:24:14
description: 用GitHub做一个免费的图床 
tags:
- 工具
cover: https://i.loli.net/2021/06/15/t2IozZFGbgjOcCW.jpg
# 置顶优先级 数值越大优先级越高 #
sticky: 11111
# 【可选】文章分类 #
categories: GitHub
# 【可选】文章关键字 #
keywords:
- 图床
- GitHub
date: 2021-06-15 22:31:22
  
---
---


### 创建github仓库
![](https://i.loli.net/2021/06/15/tzgxAqSw798dl4u.png)
### 下载工具PicGo
{% btn 'https://github.com/Molunerfinn/PicGo',GitHub PicGo,far fa-hand-point-right,green larger %}

> PicGo是一个用于快速上传图片并获取图片`URL`链接的工具

![](https://i.loli.net/2021/06/15/POXLWE2nDGR6kza.png)
### 获取仓库 access tokens
> `Settings` -> `Developer settings` -> `Personal access tokens`

![](https://i.loli.net/2021/06/15/9gtF6rSQqR1lLXG.png)
#### 点击 `Generate new token` 获取token
> 将token保存至安全的地方,这里的token只会显示一次（务必保存好）

### 配置PicGo中的GitHub图床
![](https://i.loli.net/2021/06/15/QvjSVGHT21IJ8e5.png)

### 查看图床列表
![](https://i.loli.net/2021/06/15/cKCuW1oERV5xmQy.png)

### 使用其他图床
> 也可以使用其他图床，比如sm.ms
> sm.ms 有5GB的免费空间，短时间内是够用的，速度也还可以
- 注册后在控制台获取token
  ![](https://i.loli.net/2021/06/15/tF174HUcOQTGBPk.png)
- 在PicGo中配置即可
  ![](https://i.loli.net/2021/06/15/gJIUt27vYFpE8Hx.png)
- 上传时在这里选择源就行
  ![](https://i.loli.net/2021/06/15/cZuOj4l7oAbTwsy.png)