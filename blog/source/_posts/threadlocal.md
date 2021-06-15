---
title: ThreadLocal
tags: []
id: '1408'
categories:
  - - java
date: 2020-04-24 21:40:23
---

先上两个BUG

BUG\_1:

```
package ThreadLocal;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * @Author: 赵博雅
 * @Date: 2020/4/24 15:49
 *
 * String类型转Date类型（format）
 */
public class SimpleDateFormatBugTest {

    private static SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    private static Date parse(String date){
        try {
            return sdf.parse(date);
        }catch (ParseException e){
            e.printStackTrace();
        }
        return null;
    }

    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            Date parse = parse("2020-04-24 15:54:59");
            System.out.println("第一次执行时间：" + parse);
        });

        Thread thread2 = new Thread(() -> {
            Date parse = parse("2020-04-25 15:54:59");
            System.out.println("第二次执行时间：" + parse);
        });

        Thread thread3 = new Thread(() -> {
            Date parse = parse("2020-04-26 15:54:59");
            System.out.println("第三次执行时间：" + parse);
        });

        thread1.start();
        thread2.start();
        thread3.start();

        try {
            thread1.join();
            thread2.join();
            thread3.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("线程退出");
    }

}
```

![](https://www.zby123.club/wp-content/uploads/2020/04/ThreadLocal1-1024x652.png)

多线程下使用同一个SimpleDateFormat实例将String日期格式转换成Date只能获取到其中一次的结果，其他两个线程出错

BUG\_2:

```
package ThreadLocal;

import java.text.ParseException;
import java.util.Date;

/**
 * @Author: 赵博雅
 * @Date: 2020/4/24 16:04
 * <p>
 * Date日期转String类型（format）
 */
public class SimpleDateFormat {

    private static java.text.SimpleDateFormat sdf = new java.text.SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    private static Date d1 = null;
    private static Date d2 = null;
    private static Date d3 = null;

    static {
        try {
            d1 = sdf.parse("2020-04-24 15:54:59");
            d2 = sdf.parse("2020-04-25 15:54:59");
            d3 = sdf.parse("2020-04-26 15:54:59");
        } catch (ParseException e) {
            e.printStackTrace();
        }
    }
    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            String format = sdf.format(d1);
            System.out.println("第一次执行时间：" + format);
        });

        Thread thread2 = new Thread(() -> {
            String format = sdf.format(d2);
            System.out.println("第二次执行时间：" + format);
        });

        Thread thread3 = new Thread(() -> {
            String format = sdf.format(d3);
            System.out.println("第三次执行时间：" + format);
        });
        
        thread1.start();
        thread2.start();
        thread3.start();
        
        System.out.println("线程退出");
    }
}
```

![](https://www.zby123.club/wp-content/uploads/2020/04/ThreadLocal2-1024x536.png)

同样在多线程下如果将Date转成String的话，多个线程共享了同一个数据，导致获取的时间为同一个数据

#### 解决方案一：ThreadLocal

![](https://www.zby123.club/wp-content/uploads/2020/04/ThreadLocal3-1024x731.png)

Result：

![](https://www.zby123.club/wp-content/uploads/2020/04/ThreadLocal4-1024x470.png)

#### 原因分析：

SimpleDateFormat是线程不安全的，具体可以从parse_(_source, pos_)_方法中得到答案

```
parsedDate = calb.establish(calendar).getTime();
```

calendar是成员变量，成员变量在多线程下如果没有锁的限制的话，就很容易出现线程安全问题。

那使用synchronized也可以做到线程安全，但是锁会降低线程的执行效率

#### 解决方案二：synchronized

![](https://www.zby123.club/wp-content/uploads/2020/04/ThreadLocal7.png)