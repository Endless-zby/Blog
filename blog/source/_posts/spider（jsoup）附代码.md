---
title: Spider（jsoup）附代码
updated: 2021-06-13 00:24:14
description: Java爬虫 | 使用jsoup爬取中国青年网的资讯
tags:
- Java
# 置顶优先级 数值越大优先级越高 #
sticky: 9905
cover: https://i.loli.net/2021/06/25/gpQBb6ETlZxPtdj.jpg
top_img: https://i.loli.net/2021/06/25/DSKwZHctTpUaLm5.png
# 【可选】文章分类 #
categories: Java
# 【可选】文章关键字 #
keywords:
- Spider
- 爬虫
date: 2019-11-06 10:41:57
---

> 使用jsoup爬取中国青年网的资讯（资讯列表，资讯内容）

### 思路分析

> [http://news.youth.cn/gn/](http://news.youth.cn/gn/)网站内容分析，每一条资讯单独成行，在标签` <ul class="tj3\_1"> `中，内容比较格式化，只需要将页面下载下来，以行为单位进行逐行解析即可

### 流程

![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023320-145927-uqhg9kjsoxspider.bmp)

###  代码思路

- 为了保证代码的易用性和高度灵活性，使用工厂模式是一个好的思路，将各个模块独立出来，以链式结构创建和初始化可以自由搭配所需的组件。

- mapper接口使用泛型，可以保证在使用新的解析方法后不用重写数据服务代码。

- 工厂模式方便创建多线程任务

### 代码（只贴 Factory 和Main）

- Main：

```java
import Util.MyLogger;
import Util.MySpiderContextFactory;
import MySpider.MySpiderContext;
import java.net.URL;
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * @Author: 赵博雅
 * @Date: 2019/10/31 11:29
 */
public class ContextMain {

    private static URL[] urls;
    public static boolean useThreads = false;

    public static void main(String[] args) throws Exception{


        MyLogger.log("获取所有待爬取的URL");
        urls = new URL[500];
        YouthNewsService youthNewsService = new YouthNewsService();
        youthNewsService.init();
        List<YouthNews> youthNews = youthNewsService.selectList();
        MyLogger.log("队列初始大小：" + urls.length);
        MyLogger.log("获取文章数量：" + youthNews.size());

        for (int i = 0; i < youthNews.size(); i++) {
            urls[i] = new URL("http://" + youthNews.get(i).getUrl());
        }


        long start = System.currentTimeMillis();
        //单线程启动demo
        if(!useThreads){
            //从工厂类中获得一个爬虫实例
            MySpiderContext mySpider = MySpiderContextFactory.getNewsContextBySpider(urls);
            mySpider.start();

        }
        //多线程启动demo

        long end = System.currentTimeMillis();
//        MyLogger.log("系統运行时间(除线程等待休眠时间)：" + (end - start - ((urls.length-1) * 1000 * 60)) + " ms");
        MyLogger.log("Main ended");


    }
}
```

- Factory：

```java
package club.zby.Until;


import club.zby.Boot.Example.ExampleBoot;
import club.zby.DataService.Example.exampleDataService;
import club.zby.DataService.NewsContextService;
import club.zby.Downloader.Example.StreamDownloader;
import club.zby.Processor.Example.NewsProcessor;
import club.zby.Processor.Example.YouthProcessor;
import club.zby.ScheduleQueue.Example.exampleScheduleQueue;
import club.zby.Spider.MySpider;
import club.zby.Spider.MySpiderContext;

import java.net.URL;

/**
 * @Author: 赵博雅
 * @Date: 2019/10/31 16:20
 */
public class MySpiderContextFactory {


    /**
     * 返回一个“中国青年网”的网络爬虫实例（非单例,单线程服务，存储数据至本地临时文件）
     * @return
     */
    public static MySpiderContext getSpiderContextToLocalAndNoDataService(URL[] urls) throws Exception {
        MySpiderContext mySpiderContext = new MySpiderContext(urls)
                .addBoot(new ExampleBoot())
                .addDownloader(new StreamDownloader())
                .addProcessor(new NewsProcessor())
                .addScheduleQueue(new exampleScheduleQueue());
        return mySpiderContext;
    }

    /**
     * 返回一个“中国青年网”的网络爬虫实例（非单例,单线程服务，存储数据至本地mysql数据库）
     * @return
     */
    public static MySpiderContext getSpiderDataToDataService(URL[] urls) throws Exception {
        MySpiderContext mySpiderContext = new MySpiderContext(urls)
                .addBoot(new ExampleBoot())
                .addDownloader(new StreamDownloader())
                .addProcessor(new NewsProcessor())
                .addScheduleQueue(new exampleScheduleQueue())
                .addDataService(new NewsContextService());
        return mySpiderContext;
    }
    
}
```

###  结果

![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023320-145936-zza6qy60u5spiderdemo1.png)

### 资讯列表

![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023320-145947-zv5woo3iqespiderdemo2.png)

### 列表中每条资讯的详细内容

### 完整代码，点击下面的按钮下载（需要根据entity中的实体自己建立数据）

[点击此处下载](https://pan.baidu.com/s/1MKPHuF49rwGlHhDbr2FZIA)
> 密码：0kad
