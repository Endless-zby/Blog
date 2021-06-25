---
title: maven自动处理受损jar包
updated: 2021-06-13 00:24:14
description: maven自动处理受损jar包
tags:
- maven
# 置顶优先级 数值越大优先级越高 #
sticky: 9909
# 【可选】文章分类 #
categories: maven
cover: https://i.loli.net/2021/06/25/eBtHPNM1Z4vTwbn.jpg
top_img: https://i.loli.net/2021/06/25/du6ekAfIZ4jvYRB.jpg
# 【可选】文章关键字 #
keywords:
- maven
date: 2019-04-25 22:13:47
---

> 保存以下代码为 `.bat` 文件双击运行即可
#### `REPOSITORY_PATH`是本地maven仓库路径地址

```bash
set REPOSITORY_PATH=F:\maven\MavenRepository
rem 正在搜索...
for /f "delims=" %%i in ('dir /b /s "%REPOSITORY_PATH%\*lastUpdated*"') do (
    del /s /q %%i
)
rem 搜索完毕
pause
```

