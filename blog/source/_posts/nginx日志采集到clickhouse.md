---
layout: posts
title: nginx日志采集到clickhouse(vector)
date: 2022-10-29 23:03:41
updated: 2022-10-29 23:03:41
top_img: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221029-v85jtcx7m4clickhouse-vector.png
cover: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221029-v85jtcx7m4clickhouse-vector.png
description: 尝试将nginx的日志通过vector写入clickhouse，使用grafana对clickhouse数据进行可视化分析
tags:
- Linux
- 工具
- bash
# 置顶优先级 数值越大优先级越高 #
sticky: 100060
# 【可选】文章分类 #
categories: Linux
# 【可选】文章关键字 #
keywords:
- vector
- grafana
- clickhouse
---

## 主要工具介绍

### vector
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221029-695heynk694Z4%7BS~0BWP6S6B%281JCF17%40K.png)

> vector 是基于rust 编写的高性能，数据可视化平台，支持数据的聚合以及可视化 
> Vector 是一种高新能端到端的日志（logs）、指标（metrics）、跟踪信息（traces）数据同步管道，将原始数据通过聚合或者重构后写入到想要的存储中；Vector 通过链式的简单配置（toml）可以对数据源、解析器、输出端做出你想要的任何变化，可以实现显着的成本降低、丰富的数据处理和数据安全；且开源，比所有替代方案（如logstash）快 `10` 倍。
> 案例：https://cloud.tencent.com/developer/article/2086757
{% btn 'https://vector.dev/',vector官网,far fa-hand-point-right,green larger %}

看一张性能对比图
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-23g48wnayx33fdc0e0518051302cc1eda44b2a45dc.png)


## 目标
> 主要目标是利用grafana对nginx的日志数据进行可视化分析，但nginx产生的数据量在非常大的情况下，我们选择的数据库是不是合理的将会对图表数据的获取产生比较大的影响，列式的数据结构更加适合统计图对数据的要求，选择clickhouse作为nginx日志的持久化数据库非常合适

## nginx 日志格式化
> 先对nginx access日志json格式化，如果对remap熟悉的话，可以在vector配置中transforms阶段来对日志做变换
```json
log_format aka_logs
    '{"timestamp":"$time_iso8601",'
    '"host":"$hostname",'
    '"server_ip":"$server_addr",'
    '"client_ip":"$remote_addr",'
    '"xff":"$http_x_forwarded_for",'
    '"domain":"$host",'
    '"url":"$uri",'
    '"referer":"$http_referer",'
    '"args":"$args",'
    '"upstreamtime":"$upstream_response_time",'
    '"responsetime":"$request_time",'
    '"request_method":"$request_method",'
    '"status":"$status",'
    '"size":"$body_bytes_sent",'
    '"request_body":"$request_body",'
    '"request_length":"$request_length",'
    '"protocol":"$server_protocol",'
    '"upstreamhost":"$upstream_addr",'
    '"file_dir":"$request_filename",'
    '"http_user_agent":"$http_user_agent"'
  '}';
```
## 安装Vector

### 使用脚本安装
```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.vector.dev | bash
```
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-in6cp9jsl7XQZM7NAPJ%5DLECV1I%25%7BY_%28N5.png)

### 配置
> sinks先配置为print，将读取并处理后的log文件内容显示在控制台
> remap(VRL语法参考官方文档):https://vector.dev/docs/reference/vrl/

```toml
[sources.nginx_access_log]
type = "file"
include = ["/www/wwwlogs/*_access.log"]
read_from = "end"

# Parse Syslog logs
# See the Vector Remap Language reference for more info: https://vrl.dev
[transforms.nginx_parse_logs]
type = "remap"
inputs = ["nginx_access_log"]

# 如果数据源是file类型的话vector默认会加上一些参数，比如`file` `host` `source_type` `timestamp` `message` ,message中的参数才是nginx中定义的日志结构，这里我们只取message
source = '''
. = parse_json!(.message)
'''

# Print parsed logs to stdout
[sinks.print]
type = "console"
inputs = ["nginx_parse_logs"]
encoding.codec = "json"

# Vector's GraphQL API (disabled by default)
# Uncomment to try it out with the `vector top` command or
# in your browser at http://localhost:8686
#[api]
#enabled = true
#address = "127.0.0.1:8686"
```
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-sb5ims03ro4%7D8NEJ%5D%24RVUS2NI1J1WZ2A7.png)


### vector启动

```shell
./vector --config config/vector.toml
```
访问下网站看看vector的输出是不是符合预期结果的
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-ukz3x0ekudYTD3P0JY4N%40XIISHKJS%256~D.png)


## 安装clickHouse

### 安装命令
```shell
sudo apt-get install apt-transport-https ca-certificates dirmngr

sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv E0C56BD4

echo "deb https://repo.clickhouse.tech/deb/stable/ main/" | sudo tee \
    /etc/apt/sources.list.d/clickhouse.list
    
sudo apt-get update

sudo apt-get install -y clickhouse-server clickhouse-client

sudo service clickhouse-server start

# 连接
clickhouse-client
```
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-n0cwe7gnwq%24QYYZBM6F8UV6FKOGM7RV~5.png)

> clickhouse默认安装后没有密码，后面需要添加密码 clickhouse-server启动后默认是只能本地访问，需要修改listen地址

### clickHouse配置

- 设置密码
```shell
vim /etc/clickhouse-server/users.xml
```
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-cxtr6r7sr8Z1XW4%5D21%5D%60%25%7D%24IMN6XS1VUQ.png)

- 打开远程访问
```shell
vim /etc/clickhouse-server/config.xml
```
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-cbzhxws6mrK6%40%7BO15W1WHYFI%7DF_X%60%288FL.png)

- 开放8123、9000端口
```shell
ufw status

ufw allow 8123

ufw allow 9000
```
- http面板
```http request
http://127.0.0.1:8123/play
```
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-njf5y68jxs9C_HJZ_5NNVDJA86Z%280OY72.png)

- 连接测试
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-d8xb59ur3i6%5DN5JAI9VL%2468%5D65%7DT~ST%24P.png)

### 建表(根据nginx日志结构建表)
> 根据nginx日志结构建表(nginx_log)
```clickhouse
-- `default`.nginx_log definition

CREATE TABLE `default`.nginx_log (
	`timestamp` VARCHAR,
	host VARCHAR,
	server_ip VARCHAR,
	client_ip VARCHAR,
	xff VARCHAR,
	`domain` VARCHAR,
	url VARCHAR,
	referer VARCHAR,
	args VARCHAR,
	upstreamtime VARCHAR,
	responsetime VARCHAR,
	request_method VARCHAR,
	status VARCHAR,
	`size` VARCHAR,
	request_body VARCHAR,
	request_length VARCHAR,
	protocol VARCHAR,
	upstreamhost VARCHAR,
	file_dir VARCHAR,
	http_user_agent VARCHAR
) ENGINE = Log;
```
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-j9282d30twM6Y%40M0%7DL40~S%5BTRGKFYABPD.png)

## 修改Vector sinks为clickhouse
```toml
# ...这是之前的配置

[sinks.clickhouse]

type = "clickhouse"

inputs = ["nginx_client_ip"]

endpoint = "http://127.0.0.1:8123"

database = "default"

table = "nginx_log"

skip_unknown_fields = true


# clickhouse 连接配置

[sinks.clickhouse.auth]
    # The authentication strategy to use.
    #
    # * required
    # * type: string
    # * must be: "basic"
    strategy = "basic"

    # The basic authentication password.
    #
    # * required
    # * type: string
    # * required when strategy = "basic"
    password = "zby123456"

    # The basic authentication user name.
    #
    # * required
    # * type: string
    # * required when strategy = "basic"
    user = "default"

```
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-sljys4wwg31%7DYHNBGHG%28%5BDJ%287I2LJW2%24M.png)

## 启动vector(将日志同步到clickhouse)
```shell
./vector --config config/vector.toml
```
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-qjwyu49qp4A98JC8D0%40BYAX33TPZ%7DALGP.png)

查看nginx_log表里有没有数据
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-mikaq3yudj%5DNTK9%5D1W%40J032UWLBR2EZVA.png)

可以看到nginx_log表中已经有了nginx的日志数据

## grafana 建立数据图表

### 安装clickhouse插件
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-dj24ci44n8E_V5FSKKO2ST8%247NJ~4B8DA.png)

### 添加clickhouse数据源
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-7zmc2cqy7j%5D0AAL%29GMIYO8BD8%40%7DZ%29%60YE8.png)


### 创建dashboard
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-hcre8mn8ff90VSIL%5DDSR%7DP%5DGO2AYU8C34.png)
![](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/20221030-9oyac9veklC97WNVL%5D9BJ4YZQB%7DT%28%28WYT.png)

## 写在最后
在有大量的日志数据的支撑下，使用grafana可以对整个平台的请求情况进行很直观的统计和刻画，借助vector我实现了将nginx日志实时同步至clickhouse中;
grafana官网提供了一些数据源的公共面板模板，支持一键导入，地址：https://grafana.com/grafana/dashboards/
