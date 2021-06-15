---
title: Android跨平台投屏（ScreenToGif）
date: 2019-08-06 12:56:32
updated: 2021-05-23 00:24:14
description: 推荐一款开源投屏录屏工具
tags:
    - 工具
# 置顶优先级 数值越大优先级越高 #
sticky: 9970
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
    - 投屏
    - ScreenToGif
---
---
## 官网
[官网](https://www.screentogif.com/)

## 项目
[项目](https://github.com/NickeManarin/ScreenToGif)


在众多的投屏工具中这是我觉得最好用的一款，响应速度快，功能齐全，没有广告

## 安装环境

### 下载ADB
需要配置ADB调试环境

[ADB下载](https://pan.baidu.com/s/1bT9HzANDPVR3l3eVy6ZYuw)

解压密码：`123`

### 配置环境变量

### 开发者模式下连接至电脑

以下是一些参数配置，需要自定义的话可以看看

*   如果有多个设备，需要指定序列号，序列号可以从adb devices获得

```shell
scrcpy -s a1171b8
```

*   设置端口
```shell
scrcpy -p 27184
```

*   查看帮助

```shell
scrcpy --help
```


*   设置码率（默认8M）

 ```shell
 scrcpy -b 8M
 ```

*   限制投屏尺寸

 ```shell
 scrcpy -m 1024
 ```
 

* 裁剪投屏屏幕(长:宽:偏移x:偏移y)

 ```shell
 scrcpy -c 800:800:0:0
 ```
 

*   投屏并录屏

 ```shell
 scrcpy -r file.mp4
 ```
 

*   不投屏只录屏

 ```shell
 scrcpy -Nr file.mp4
 ```
 

*   手指触摸的时候显示轨迹球

 ```shell
 scrcpy -t
 ```
 

*   显示版本信息

 ```shell
 scrcpy -v
 ```
 
