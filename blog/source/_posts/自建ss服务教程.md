---
title: 搭建自己的ssr
date: 2019-10-12 12:05:16
updated: 2021-05-16 01:50:56
top_img: https://i.loli.net/2021/06/19/EBn5ciPYWOzIaNq.jpg
cover: https://i.loli.net/2021/06/19/By7GVLIvDwfepKb.jpg
description: 相比市场上较高投入的虚拟专用网络，只是单纯的用于工作的话，不妨自己试着搭建一套ssr
tags: 
 - 工具 
# 置顶优先级 数值越大优先级越高 #
sticky: 100001
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords: 
 - ssr
 - vps
 - vultr
---

> 富强、民主、文明、和谐，自由、平等、公正、法治，爱国、敬业、诚信、友善


*   第一步：**购买VPS服务器**
*   第二步：**部署VPS服务器**
*   第三步：**加速VPS服务器**

## 寻找一个合适的vps服务器

### 试试vultr
VPS服务器需要选择国外的，首选当然是 [vultr](https://my.vultr.com/)

原因：全球机房多，有较多的选择，当然吸引我的主要原因还是因为定期有较多的活动，优惠和稳定才是王道，以上原因归于一个字：穷
### 费用
使用邮箱注册账号登录，购买服务器的支付途径有微信和支付宝，不需要绑定信用卡（对，我说的就是亚马逊），服务器的费用是按小时收费的，如果选择目前最低价的话每小时$0.01，当然作为新人注册，再遇到有优惠活动的话就可以享受到$2.5每月的折扣，并且充$50送$50，这个就得你自己多关注了
### 创建
下面我们来看如何创建自己的VPS服务器

![](https://i.loli.net/2021/06/17/7c8fxQMpDCOgrHU.png)

![](https://i.loli.net/2021/06/17/6k4rUWMloIGabZF.png)

![](https://i.loli.net/2021/06/17/9Iixu1sW2EyHtqe.png)

联通的网络的话选择美国西雅图的节点会快点，延迟在 __60~75__ 之间，也比较稳定  

最近日本的节点大量不可用，有时间了多开几个机器试试  

日本虽然距离近但是所有的节点得绕美国，还是直接选美国吧

![](https://i.loli.net/2021/06/17/eiIRfGTS79obnLZ.png)

最好选择**contos7**

![](https://i.loli.net/2021/06/17/bz3MSkxA9l2BVXa.png)

这些用不着，不用填

点击创建服务器后，我们等待服务器注册启动

启动完成后可以在控制台看到已经有一台机器在运行了

![](https://i.loli.net/2021/06/17/5BHOiqWeDI2dona.png)

点进去可以看到当前运行服务的主要信息

![](https://i.loli.net/2021/06/17/SMWo85gcXlqLm7N.png)

使用 __xshell__ 来连接这台服务器

![](https://i.loli.net/2021/06/17/QKCBtLq19iueHmN.png)

![](https://i.loli.net/2021/06/17/Pk9V6ftBa8LoCyY.png)

填写完成后点击连接即可
### 注意
如果遇到无法连接话，很大概率是被墙了，那就需要更换ip，按照刚才创建服务器步骤再来一遍，选择其他节点（需要注意的是，在创建新的服务器之前，不要销毁刚才的服务器，因为如果先销毁再创建，得到的ip还是原来的那个，所以选择先创建再销毁）

![](https://i.loli.net/2021/06/17/w3cxisvWa6OrUCV.png)

连接成功

以上就是创建连接SVP服务器的步骤

## 部署SVP服务器

> 开始部署ss环境
> 
> 以下照做就是了

为避免一些不必要的麻烦先更新内核版本

``` shell
yum update
```

下载安装shadowsocks

```shell
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocksR.sh
```

如果遇到未知命令问题，那就先安装wget，如果没有问题，跳过这一步

```shell
yum -y install wget
```

设置启动权限

```shell
chmod +x shadowsocksR.sh
```

启动安装配置

```shell
./shadowsocksR.sh 2>&1  tee shadowsocksR.log
```

![](https://i.loli.net/2021/06/17/eGaopXjPQYqW5s2.png)

设置连接密码

![](https://i.loli.net/2021/06/17/5dvhciXru362xHf.png)

设置访问端口

回车开始安装

![](https://i.loli.net/2021/06/17/fy8N6GhnoCgRbsL.jpg)

安装完成  
上面的信息保存好

到这里其实就已经完成了vps服务器的部署，但是为了保证网络的稳定和较低的延迟，再配置上加速服务加速服务选择破解版的锐速加速器

## 配置锐速加速器

执行

```shell
wget --no-check-certificate -O rskernel.sh https://raw.githubusercontent.com/hombo125/doubi/master/rskernel.sh && bash rskernel.sh
```

![](https://i.loli.net/2021/06/17/itm8rnQqTBNwYds.png)

内核更新完成后，系统重启，一分钟后重新连接xshell

等待内核更换完毕后系统会自动重启并断开连接。然后重新连接，执行下面命令

```
yum install net-tools -y && wget --no-check-certificate -O appex.sh https://raw.githubusercontent.com/0oVicero0/serverSpeeder_Install/master/appex.sh && bash appex.sh install
```

![](https://i.loli.net/2021/06/17/p1dWxCGojPsFeaE.png)

系统会自动安装锐速，同时会先后要求我们设置锐速的三项信息  
按照图上的设置就行了

![](https://i.loli.net/2021/06/17/TymzP6cZnxq19ga.png)

看到serverSpeeder is running那就证明成功了  

## SSR客户端工具下载

 * [github 选择最新版本](https://github.com/shadowsocksr-backup)

 * [Windows版本点击下载](https://github.com/shadowsocksr-backup/shadowsocksr-csharp/releases)

 * [安卓版本点击下载](https://github.com/shadowsocksr-backup/shadowsocksr-android/releases/download/3.4.0.8/shadowsocksr-release.apk)

 * [Mac版本点击下载](https://github.com/shadowsocksr-backup/ShadowsocksX-NG/releases)

## Windows连接示例

![](https://i.loli.net/2021/06/17/WDYN8jQ3g7oxhX9.png)

无压力播放1080p视频

应对平时一般的资料查找或者。。。。足够了，也可以添加多个端口和密码，供多个设备使用

如果还有疑问也可以联系本人 381016296@qq.com
