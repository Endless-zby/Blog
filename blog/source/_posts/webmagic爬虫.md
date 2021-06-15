---
title: WebMagic爬虫
updated: 2021-06-14 00:24:14
description: WebMagic爬虫
tags:
- Java
# 置顶优先级 数值越大优先级越高 #
sticky: 9968
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- Java
- 爬虫
- WebMagic
date: 2019-07-10 19:25:45
---

> A simple, scalable, and highly efficient web crawler framework for Java.

*   WebMagic基于Maven进行构建，推荐使用Maven来安装WebMagic。在你自己的项目（已有项目或者新建一个）中添加以下坐标即可：

```xml
<dependency>
    <groupId>us.codecraft</groupId>
    <artifactId>webmagic-core</artifactId>
    <version>0.7.3</version>
</dependency>
<dependency>
    <groupId>us.codecraft</groupId>
    <artifactId>webmagic-extension</artifactId>
    <version>0.7.3</version> 
</dependency>
```


>  在你的项目中添加了WebMagic的依赖之后，即可开始第一个爬虫的开发了！我们这里拿一个抓取Boss直聘网招聘信息的例子：

```java
package com.zby.test;

import us.codecraft.webmagic.Page;
import us.codecraft.webmagic.Site;
import us.codecraft.webmagic.Spider;
import us.codecraft.webmagic.processor.PageProcessor;

public class spider implements PageProcessor {

    @Override
    public void process(Page page) {
//        System.out.println( "信息：" +page.getHtml().xpath("//*[@id='Main']/div[1]/div[2]/ul/li[1]/div/div[1]/p/text()") .toString()  );
//        System.out.println( "公司：" +page.getHtml().xpath("//*[@id='Main']/div[1]/div[2]/ul/li[1]/div/div[2]/div[1]/h3/a/text()") .toString()  );
//        System.out.println( "类型：" +page.getHtml().xpath("//*[@id='Main']/div[1]/div[2]/ul/li[1]/div/div[2]/div[1]/p/text()") .toString()  );
        System.out.println( "职位：" +page.getHtml().xpath("//*[@id='main']/div[1]/div[1]/div[1]/div[2]/div[2]/h1/text()") .toString()  );
        System.out.println( "薪资：" +page.getHtml().xpath("//*[@id='main']/div[1]/div[1]/div[1]/div[2]/div[2]/span/text()") .toString()  );
        System.out.println( "学历：" +page.getHtml().xpath("//*[@id='main']/div[1]/div[1]/div[1]/div[2]/p/text()") .toString()  );
        System.out.println( "福利：" +page.getHtml().xpath("//*[@id='main']/div[1]/div[1]/div[1]/div[2]/div[3]/div[2]/span[1]/text()") .toString()  );


    }

    @Override
    public Site getSite() {
        return Site.me().setSleepTime(1000).setRetrySleepTime(5);

    }


    public static void main(String[] args) {
//        Spider.create(new spider()).addUrl("https://www.zhipin.com/c101010100/?ka=open_joblist").run();
        Spider.create(new spider()).addUrl("https://www.zhipin.com/job_detail/3ded0d912dac595903V53tu8E1o~.html?ka=search_list_1").run();
    //27
    }
}
```

![](https://zby123.club/wp-content/uploads/2019/07/webmagic-1024x174.png)

```java
public Site getSite() {
         return Site.me().setSleepTime(1000).setRetrySleepTime(5);
}
```

> 这里是防止被网站后台发现爬取内容，加入等待时间