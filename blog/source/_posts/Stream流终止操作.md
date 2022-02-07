---
title: Stream流终止操作
date: 2021-07-12 16:44:32
updated: 2021-07-12 16:44:32
top_img: https://i.loli.net/2021/07/20/3lPiL2pXRuTon5I.jpg
cover: https://i.loli.net/2021/07/20/3lPiL2pXRuTon5I.jpg
description: stream has already been operated upon or closed
tags: 
 - Java 
# 置顶优先级 数值越大优先级越高 #
sticky: 99991
# 【可选】文章分类 #
categories: Java
# 【可选】文章关键字 #
keywords: 
 - Stream
---

{% note blue 'fas fa-bullhorn' simple %}
`Stream流` 类似于杯中的水，当发生把水倒出的行为（终止操作）时，就会空杯，无法再进行其他操作。
{% endnote %}
---
## 例

![](https://i.loli.net/2021/07/12/9Ay56emBOhNop28.png)
> `contractMarkingList`数据流 在管道执行完`findFirst()`之后,数据流已经不存在了，所以后面的`collect(toList())`所操作的就是一个已经关闭了的管道

## 报错
![](https://i.loli.net/2021/07/12/z13QbyVEF8vHMZj.png)

## 解决
> 重新建立新的stream即可
