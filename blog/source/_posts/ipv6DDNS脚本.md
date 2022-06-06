---
title: 腾讯云DDNS动态解析ipv6地址
date: 2022-06-06 20:30:21
updated: 2022-06-06 20:30:21
top_img: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202266-xdkjq3edzcdns8.jpeg
cover: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202266-mhd924sogzdns7.jpeg
description: 目前中国移动宽带是支持ipv6的，如果是安装的是移动宽带，就可以给自己的所有设备分配到ipv6公网地址，不用再搞什么内网穿透，公网速度直接取决于带宽上行速度，简直完美，但是目前大多ipv6地址是动态的，所以得想办法把动态的ipv6地址解析到一个固定的域名上
tags:
- Linux
- 工具
- bash
# 置顶优先级 数值越大优先级越高 #
sticky: 100035
# 【可选】文章分类 #
categories: Linux
# 【可选】文章关键字 #
keywords:
- DDNS
- ipv6
---


> 目前中国移动宽带是支持ipv6的，如果是安装的是移动宽带，就可以给自己的所有设备分配到ipv6公网地址，不用再搞什么内网穿透，公网速度直接取决于带宽上行速度，简直完美，但是目前大多ipv6地址是动态的，所以得想办法把动态的ipv6地址解析到一个固定的域名上

### ipv6检查

可以去test-ipv6网站查一下
{% btn 'https://test-ipv6.com/',IPv6 连接测试,far fa-hand-point-right,green larger %}
如果是这样的结果就说明你的宽带以及运营商是支持ipv6并分配给你了可使用的ipv6地址，就可以看下面的操作了
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202266-8py59ww3vpdns4.png)
### 注册域名

> 域名注册比较简单，但是可能需要一段时间进行实名认证的审核，现在好像可以在手机上一键认证，速度会比较快

{% btn 'https://buy.cloud.tencent.com/domain?domain=&tlds=&from=dnspodEntrance',腾讯云域名注册申请,far fa-hand-point-right,orange larger %}
如果是打算长期作为自己工作站或者NAS使用的话没必要购买`.cn` `.com`这类域名，第一次买可能会便宜点，后面每年的费用都会有一定幅度的增长，这种情况下建议购买`.xyz`  `.work`  `.club`这种相对冷门的域名，如果有钱或者想要打造自己的一个招牌的话忽略这个建议。

### 添加域名解析

添加一条A记录 记录值随便填一个规范的ip地址
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202266-tsewb2mbtsdns1.png)
### 脚本

脚本中需要关注的参数有这么几个
dnspod的id和token，登录dnspod，可以从腾讯云域名管理页面进入，点击创建密钥，自定义名称然后就创建好了，保存好弹出的id和token，因为这个只显示一次，关闭页面之后就看不到了
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202266-322n1l4g8edns2.png)
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202266-kg54xije91dns3.png)
设置根域名和自定义二级域名，填到脚本中的对应参数中，最好再检查下自己的网卡名称是不是默认的eth0，不是的话改成自己的网卡名称。

脚本如下，保存后记得设置文件权限，设置脚本运行时间大概是每十分钟运行一次，对比现在的ipv6地址和dns解析的值是不是一样的，不一样的话自动修改AAAA解析值
```shell
dnspod_ddnsipv6_id="111111" #【API_id】将引号内容修改为获取的API的ID
dnspod_ddnsipv6_token="45d4fg5d5f4g5d4fg5488wert4e4" #【API_token】将引号内容修改为获取的API的token
dnspod_ddnsipv6_ttl="600" # 【ttl时间】解析记录在 DNS 服务器缓存的生存时间，默认600
dnspod_ddnsipv6_domain='aaaa.club' #【已注册域名】引号里改成自己注册的域名
dnspod_ddnsipv6_subdomain='test' #【二级域名】将引号内容修改为自己想要的名字，需要符合域名规范，附常用的规范
local_net="eth0" # 【网络适配器】 默认为eth0，如果你的公网ipv6地址不在eth0上，需要修改为对应的网络适配器

if [ "$dnspod_ddnsipv6_record" = "@" ]
then
  dnspod_ddnsipv6_name=$dnspod_ddnsipv6_domain
else
  dnspod_ddnsipv6_name=$dnspod_ddnsipv6_subdomain.$dnspod_ddnsipv6_domain
fi

die () {
    echo "not find ipv6 address"
    exit
}

ipv6s=`ip addr show $local_net | grep "inet6.*global" | awk '{print $2}' | awk -F"/" '{print $1}'` || die

for ipv6 in $ipv6s
do
    break
done

echo "Local IP: $ipv6"

dns_server_info=`nslookup -query=AAAA $dnspod_ddnsipv6_name 2>&1`

dns_server_ipv6=`echo "$dns_server_info" | grep 'address ' | awk '{print $NF}'`
if [ "$dns_server_ipv6" = "" ]
then
    dns_server_ipv6=`echo "$dns_server_info" | grep 'Address: ' | awk '{print $NF}'`
fi

if [ "$?" -eq "0" ]
then
    echo "DNS server IP: $dns_server_ipv6"

    if [ "$ipv6" = "$dns_server_ipv6" ]
    then
        echo "The address is the same as the DNS server."
		exit
    else
        unset dnspod_ddnsipv6_record_id
    fi
else
    dnspod_ddnsipv6_record_id="1"
    
fi

send_request() {
    local type="$1"
    local data="login_token=$dnspod_ddnsipv6_id,$dnspod_ddnsipv6_token&domain=$dnspod_ddnsipv6_domain&sub_domain=$dnspod_ddnsipv6_subdomain$2"
    return_info=`curl -X POST "https://dnsapi.cn/$type" -d "$data" 2> /dev/null`
}

query_recordid() {
    send_request "Record.List" ""
}

update_record() {
    send_request "Record.Modify" "&record_type=AAAA&record_line=默认&ttl=$dnspod_ddnsipv6_ttl&value=$ipv6&record_id=$dnspod_ddnsipv6_record_id"
}

add_record() {
    send_request "Record.Create" "&record_type=AAAA&record_line=默认&ttl=$dnspod_ddnsipv6_ttl&value=$ipv6"
}

if [ "$dnspod_ddnsipv6_record_id" = "" ]
then
    echo "seem exists, try update."
    query_recordid
    code=`echo $return_info  | awk -F \"code\":\" '{print $2}' | awk -F \",\"message\" '{print $1}'`
	echo "return code $code"
    if [ "$code" = "1" ]
    then
        dnspod_ddnsipv6_record_id=`echo $return_info | awk -F \"records\":.{\"id\":\" '{print $2}' | awk -F \",\"ttl\" '{print $1}'`
        update_record
        echo "update sucessful"
    else
        echo "error code return, domain not exists, try add."
	    add_record
	    echo "add sucessful."
    fi
else
    echo "domain not exists, try add."
    add_record
    echo "add sucessful"
fi
```
通过日志可以看到我的IP地址变化了一次，被侦听到了，及时修改了DNS解析值，后面的执行记录显示本地和当前DNS解析的ip地址是一致的，等到下一次本地的IP地址发生变化将会再次更新
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202266-uzfrw6pegndns5.png)

### 可能遇到的问题
#### 有ipv6地址但ping不通
检查下光猫的设置将`安全`-`防火墙等级`设置为低或者关闭，在这之后需要你有足够的网络安全意识和相关知识
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202266-gtscalvbledns6.png)
