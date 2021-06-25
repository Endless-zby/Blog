---
title: Sleep and Wait
updated: 2021-06-25 00:24:14
description: Sleep and Wait
tags:
- Java
# 置顶优先级 数值越大优先级越高 #
sticky: 9870
# 【可选】文章分类 #
categories: Java
cover: https://i.loli.net/2021/06/25/ra7BhmnSKV3LFCw.jpg
top_img: https://i.loli.net/2021/06/25/fFpQi2wJKyVLduS.jpg
# 【可选】文章关键字 #
keywords:
- Java
date: 2020-06-10 01:13:10
---

### Wait

``` java
package 锁wait;

/**
 * @Author: 赵博雅
 * @Date: 2020/6/10 0:16
 */
public class wait {

    public static void main(String[] args) {

        waitTest waitTist = new waitTest("线程1");

        synchronized (waitTist){
            String threadName = Thread.currentThread().getName();
            System.out.println(threadName + " 启动...");

            waitTist.start();

            try {
                waitTist.wait();    //主线程进入等待状态
                System.out.println(threadName + " 接着运行...");

            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class waitTest extends Thread {

    public waitTest(String name) {
        super(name);
    }

    public void run() {
        synchronized (this){
            String threadName = Thread.currentThread().getName();
            System.out.println(threadName + " 启动...");
            try {
                System.out.println(threadName + " 工作中...");
                Thread.sleep(5000);

                System.out.println(threadName + " 完成...");
                this.notify();  //使其他线程进入就绪状态
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }
}
```

![](https://i.loli.net/2021/06/25/CTrxq7Ip8JmsDFh.png)

“wait”的运行结果