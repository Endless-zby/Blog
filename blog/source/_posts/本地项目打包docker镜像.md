---
title: 本地项目打包Docker镜像
updated: 2021-06-27 00:24:14
description: 本地项目打包Docker镜像
tags:
- docker
- SpringBoot
# 置顶优先级 数值越大优先级越高 #
sticky: 9810
cover: https://i.loli.net/2021/06/27/N3QJZV9a6dR8Ecy.jpg
top_img: https://i.loli.net/2021/06/25/fFpQi2wJKyVLduS.jpg
# 【可选】文章分类 #
categories: docker
# 【可选】文章关键字 #
keywords:
- docker
date: 2019-08-14 16:24:51
---

> 上次在完成redis的测试之后说要记录一下打包docker镜像，竟然也拖到了现在
> 废话不多说，开始吧！

### docker上拉取centos7镜像

```shell
pull centos7
```

>下载linux版本的jdk（这里我使用的是jdk-8u221-linux-x64.tar.gz），上传至linux中的一个空目录（这里我建立了一个 /usr/local/jdk 的目录）
>在存放jdk的路径下新建文件`Dockerfile`

Dockerfile的内容为：

```shell
FROM centos:centos7
MAINTAINER tom
RUN mkdir /usr/local/jdk
WORKDIR /usr/local/jdk
ADD jdk-8u221-linux-x64.tar.gz /usr/local/jdk

ENV JAVA_HOME /usr/local/jdk/jdk1.8.0_221
ENV JRE_HOME /usr/local/jdk/jdk1.8.0_221/jre
ENV PATH $JAVA_HOME/bin:$PATH
```

> 这里的jdk版本以自己下载的为准，修改相关属性地址即可（from 后面是其依赖项）

> 现在我们建立了一个目录，里面有一个jdk的压缩文件和一个 Dockerfile 文件

> 在当前目录下打包jdk为docker镜像，并部署至centos7的镜像中

docker build -t= 'testall' .

{% note warning no-icon %}
注意命令的后面有个“.”
{% endnote %}


- docker images 查看

![](https://i.loli.net/2021/06/27/bY2zgVKs1yhOtZR.png)
第一个镜像就是刚才打包的jdk，最后一个就是刚才拉下来的centos7镜像

- 启动jdk镜像

docker run -it --name=testall testall /bin/bash

- 查看启动状态

![](https://i.loli.net/2021/06/27/tZDScfF1H6kajxC.png)
> 最后一个就是jdk了！

环境准备工作结束！

开始打包项目

### 在pom中引入插件

```xml
    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <mainClass>com.zby.TimeApplication</mainClass>
                    <layout>ZIP</layout>

                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```

> `<mainClass>`标签中写启动类

![](https://i.loli.net/2021/06/27/wBlUoJC3rgaxVNL.png)
> 最好先clean一下，避免不必要的错误，然后使用 package 打包jar

> 打包结束后的文件在项目路径下的`target`目录下

![](https://i.loli.net/2021/06/27/89VSFDYGgaBMQp6.png)
- 在服务器（或虚拟机）中建立一个空目录，上传打包好的jar至该目录中

- 在该目录下创建`Dockerfile`文件，内容：

```shell
From testall:testall
ADD MyTime-0.0.1-SNAPSHOT.jar /apptime.jar
EXPOSE 9007
ENTRYPOINT ["java","-jar","/apptime.jar"]
```

> `from` 后面依旧是这个jar的依赖镜像，`testall`就是刚才的jdk（刚才的命名没弄好，导致现在看着很乱，下次注意）

*   在该目录下执行命令打包镜像

```shell
docker build -t mytime:0.0.1 .
```
- 去镜像库查看

```shell
docker images
```

![](https://i.loli.net/2021/06/27/6EVZehyX2DTO9cA.png)
- 启动这个微服务

```shell
docker run -d -p 9007:9007 mytime:0.0.1
```
> 端口和项目中的yml里的服务端口一致

- 查看正在运行的镜像

![](https://i.loli.net/2021/06/27/TaAtZG5DckjueFb.png)
- 可以进行访问了

![](https://i.loli.net/2021/06/27/9D4cPRXs8nJt3xF.png)