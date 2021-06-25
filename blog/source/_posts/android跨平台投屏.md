---
title: Android跨平台投屏（scrcpy）
date: 2019-08-06 12:56:32
updated: 2021-05-23 00:24:14
description: 推荐两款开源投屏、录屏工具
tags:
    - 工具
# 置顶优先级 数值越大优先级越高 #
sticky: 9970

top_img: https://i.loli.net/2021/06/25/86oeKms2rq3CH1n.png
cover: https://i.loli.net/2021/06/25/86oeKms2rq3CH1n.png
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
    - 投屏
    - scrcpy
    - 录屏
    - ScreenToGif
---

{% note success no-icon %}
推荐两款开源`投屏工具`和`录屏工具`
{% endnote %}

{% tabs windows %}
<!-- tab Scrcpy跨设备投屏 -->

## GitHub项目Release
{% btn 'https://github.com/Genymobile/scrcpy/releases',GitHub项目Release,far fa-hand-point-right,orange larger %}

## 安装环境

### 下载ADB
需要配置`ADB`调试环境

{% btn 'https://pan.baidu.com/s/1bT9HzANDPVR3l3eVy6ZYuw',ADB下载,far fa-hand-point-right,green larger %}

{% note info no-icon %}
解压密码：`123`
{% endnote %}


### 配置环境变量

#### 开发者模式下连接至电脑

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




<!-- endtab -->

<!-- tab ScreenToGif录屏工具 -->

## ScreenToGif官网
{% btn 'https://www.screentogif.com/',ScreenToGif官网,far fa-hand-point-right,green larger %}

## GitHub项目
{% btn 'https://github.com/NickeManarin/ScreenToGif',GitHub项目,far fa-hand-point-right,orange larger %}

## 如何使用

{% btn 'https://www.screentogif.com/features',去了解ScreenToGif,far fa-hand-point-right,blue larger %}


{% note success no-icon %}
在众多的屏幕录制工具中这是我觉得最好用的一款，{% label 免费 pink %}，{% label 简约 blue %}，{% label 录制编辑 orange %}，{% label 没有广告 green %}
{% endnote %}


<!-- endtab -->

{% endtabs %}








 
