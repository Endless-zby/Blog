---
title: Stream
updated: 2021-06-13 00:24:14
description: Java Stream
tags:
- Java
# 置顶优先级 数值越大优先级越高 #
sticky: 9901
top_img: https://i.loli.net/2021/06/25/fgtBF2W1ElapSHs.jpg
cover: https://i.loli.net/2021/06/25/epQrf8jALvUP1W2.jpg
# 【可选】文章分类 #
categories: Java
# 【可选】文章关键字 #
keywords:
- Java
date: 2020-04-08 05:53:42
---


```java
package 新特性.Stream;

import 新特性.User;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.util.*;
import java.util.stream.Stream;

import static java.util.stream.Collectors.toList;
import static java.util.stream.Collectors.toMap;

/**
 * @Author: 赵博雅
 * @Date: 2020/4/8 1:01
 */
public class Stream入门 {

    final static User w = new User("w",10);
    final static User x = new User("x",11);
    final static User y = new User("y",12);


    public static void test1(){

        ArrayList<Integer> list = new ArrayList<>();
        java.util.stream.Stream.of("abc","bcd","sddfa","sdeergth","hhhghghgh","dd","")
                .map(string -> string.length())
                .forEach(string -> list.add(string));

        System.out.println(list);

    }
    public static void test2(){

        ArrayList<Integer> list = new ArrayList<>();
        java.util.stream.Stream.of("abc","bcd","sddfa","sdeergth","hhhghghgh","dd","")
                .map(String :: length)
                .forEach(string -> list.add(string));
        System.out.println(list);
    }
    public static void test3(){

        List<Integer> collect = java.util.stream.Stream.of("abc", "bcd", "sddfa", "sdeergth", "hhhghghgh", "dd", "")
                .filter(string -> !string.isEmpty())
                .filter(string -> string.length() > 4)
                .map(String::length)
                .collect(toList());

        System.out.println(collect);
    }

    /**
     * mapToInt 将数据流中得元素转成Int，这限定了转换的类型Int，最终产生的流为IntStream，及结果只能转化成int。
     */
    public static void test4(){

        ArrayList<Integer> list = new ArrayList<>();
        java.util.stream.Stream.of("abc","bcd","sddfa","sdeergth","hhhghghgh","dd","")
                .mapToInt(String :: length)
                .forEach(string -> list.add(string));
        System.out.println(list);
    }

    public static void test5() {

        ArrayList<String> list = new ArrayList<>();

        java.util.stream.Stream.of("a-b-c-d", "e-f-i-g-h")
                .flatMap(e -> java.util.stream.Stream.of(e.split("-")))
                .forEach(e -> list.add(e));
        System.out.println(list);
    }

    /**
     * limit 限制元素的个数，只需传入 long 类型 表示限制的最大数
     */
    public static void test6() {

        List<String> collect = java.util.stream.Stream.of("abc", "bcd", "sddfa", "sdeergth", "hhhghghgh", "dd", "")
                .limit(4)
                .collect(toList());

        System.out.println(collect);
    }

    /**
     * distinct 将根据equals 方法进行判断 【去重】
     */
    public static void test7() {

        List<Integer> collect = java.util.stream.Stream.of(1, 2, 3, 1, 2, 5, 6, 7, 8, 0, 0, 1, 2, 3, 1)
                .distinct() //去重
                .collect(toList());
        System.out.println(collect);
    }

    /**
     * peek 挑选 ，将元素挑选出来，可以理解为提前消费
     */
    public static void test8() {

        List<User> collect = java.util.stream.Stream.of(w, x, y)
                .peek(e -> {if(e.getAge()>11)  e.setName(e.getAge() + e.getName());}) //重新设置名字 变成 年龄+名字
                .collect(toList());
        System.out.println(collect);
    }

    /**
     * skip 跳过 元素
     */
    public static void test9() {

        List<User> collect = java.util.stream.Stream.of(w, x, y)
                .skip(2)//跳过前2个
                .peek(e -> e.setName(e.getAge() + e.getName())) //重新设置名字 变成 年龄+名字
                .collect(toList());
        System.out.println(collect);
    }

    /**
     * sorted 排序 底层依赖Comparable 实现，也可以提供自定义比较器 -> test11
     */
    public static void test10() {
        List<Integer> collect = new ArrayList<>();
        for (Integer integer : Arrays.asList(2, 1, 3, 6, 4, 9, 6, 8, 0)) {
            collect.add(integer);
        }
        collect.sort(null);
        System.out.println(collect);
    }

    /**
     * sorted 排序 -> 自定义比较器
     */
    public static void test11() {
        User x = new User("x",11);
        User y = new User("y",12);
        User w = new User("w",10);

        List<User> collect = java.util.stream.Stream.of(w, x, y)
                .sorted((e1,e2) -> e1.getAge()>e2.getAge()?1:e1.getAge()==e2.getAge()?0:-1)
                .collect(toList());
        System.out.println(collect);
    }

    /**
     * toMap()
     */
    public static void test12() {
        User x = new User("x",11);
        User y = new User("y",12);
        User w = new User("w",10);

        Map<String, Integer> collect = java.util.stream.Stream.of(w, x, y)
                .collect(toMap(User::getName, User::getAge));
        System.out.println(collect);
    }

    /**
     * count 统计数据流中的元素个数，返回的是long 类型
     */
    public static void test13() {

        long count = java.util.stream.Stream.of(1, 2, 3, 1, 2, 5, 6, 7, 8, 0, 0, 1, 2, 3, 1)
                .distinct() //去重
                .count();
        System.out.println(count);
    }

    /**
     * findFirst 获取流中的第一个元素
     */
    public static void test14() {

        Optional<String> first = java.util.stream.Stream.of("apple", "banana", "orange", "waltermaleon", "grape")
                .findFirst();
        first.ifPresent(e->System.out.println(e));

    }

    /**
     * findAny 获取流中任意一个元素
     */
    public static void test15() {
        Optional<String> first = java.util.stream.Stream.of("apple", "banana","waltermaleon", "orange",  "grape")
                .parallel()
                .findAny();
        first.ifPresent(e->System.out.println(e));

    }

    /**
     * noneMatch 数据流中得没有一个元素与条件匹配的
     */
    public static void test16() {

        boolean result = java.util.stream.Stream.of("aa", "bb", "cc", "aa")
                .noneMatch(e -> e.equals("dd"));
        System.out.println(result);
    }

    /**
     * reduce 是一个规约操作，所有的元素归约成一个，比如对所有元素求和，乘积
     */
    public static void test17() {

        Integer reduce = java.util.stream.Stream.of(1, 5, 2, 3, 6, 5, 0, -4, 5)
                .reduce(0, (e1, e2) -> e1 + e2);
        System.out.println(reduce);
    }

    /**
     * forEachOrdered 适用用于并行流的情况下进行迭代，能保证迭代的有序性
     */
    public static void test18() {

        java.util.stream.Stream.of(1, 5, 2, 3, 6, 5, 0, -4, 5)
                .parallel()
                .forEachOrdered(e -> System.out.println(Thread.currentThread().getName() +":"+ e));

    }

    public static void main(String[] args) {

        Class c = Stream入门.class;
        Stream.of(c.getMethods())
                .filter(method -> method.getName().startsWith("test"))
                .sorted((method,method2) -> Integer.parseInt(method.getName().replaceAll("test","")) > Integer.parseInt(method2.getName().replaceAll("test","")) ? 0 :-1)
                .forEachOrdered(method -> {
                    try {
                        System.out.println("-------"+ method.getName()+"-------");
                        method.invoke(c);
                    } catch (IllegalAccessException e) {
                        e.printStackTrace();
                    } catch (InvocationTargetException e) {
                        e.printStackTrace();
                    }
                });
    }

}
```

main函数中的代码充分体现了函数式编程的代码简洁性

![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023320-150632-cnqmz2gj7vstream.png)

result：

![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023320-150639-54jh4hxelrstream1.png)
