---
title: OpenWRT中ShadowSocksR配置
date: 2022-02-15 21:34:28
updated: 2022-02-15 21:34:28
top_img: https://s2.loli.net/2022/02/14/RXQjUlSwY6DyrvM.jpg
cover: https://s2.loli.net/2022/02/14/6xyepGOPM1n5iBr.jpg
description: OpenWRT中ShadowSocksR Plus+ 支持了SS/SSR/V2RAY/TROJAN/NAIVEPROXY/SOCKS5/TUN 等通信手段，可以说拥有所有主流的代理协议,定时更新节点，节点自动切换，流量分组等功能
tags:
- Linux
- openwrt
# 置顶优先级 数值越大优先级越高 #
sticky: 100012
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- Linux
- openwrt
---

> OpenWRT中ShadowSocksR Plus+ 支持了SS/SSR/V2RAY/TROJAN/NAIVEPROXY/SOCKS5/TUN 等通信手段，可以说拥有所有主流的代理协议,定时更新节点，节点自动切换，流量分组等功能

{% btn 'https://github.com/Endless-zby/free',节点更新订阅github,far fa-hand-point-right,green larger %}
### 节点订阅URL

```http request
https://raw.fastgit.org/freefq/free/master/v2
```

### ShadowSocksR配置
![](https://s2.loli.net/2022/02/15/ubzEQx4Cg9NT7BF.png)
![](https://s2.loli.net/2022/02/15/wafPnBXktUgDFLu.png)
![](https://s2.loli.net/2022/02/15/X3NfIvzOtV67cb2.png)

配置以下禁止连接的域名，可以阻止小米设备的广告和上传用户隐私 {% btn 'https://www.zhihu.com/question/392135636',如何看待小米疑似收集手机浏览器浏览记录?
,far fa-hand-point-right,green larger %}

```http request
gvod.aiseejapp.atianqi.com
stat.pandora.xiaomi.com
upgrade.mishop.pandora.xiaomi.com
logonext.tv.kuyun.com
config.kuyun.com
mishop.pandora.xiaomi.com
dvb.pandora.xiaomi.com
api.ad.xiaomi.com
de.pandora.xiaomi.com
data.mistat.xiaomi.com
jellyfish.pandora.xiaomi.com
gallery.pandora.xiaomi.com
o2o.api.xiaomi.com
bss.pandora.xiaomi.com
```
或者使用`广告屏蔽大师`
