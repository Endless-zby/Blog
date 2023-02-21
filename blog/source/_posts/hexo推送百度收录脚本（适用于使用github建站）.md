---
layout: posts
title: hexo推送百度收录脚本（适用于使用github建站）
date: 2023-02-20 09:39:39
updated: 2023-02-20 09:39:39
top_img: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023221-181339-434dsyxfeo%E7%99%BE%E5%BA%A6%E6%94%B6%E5%BD%952.jpeg
cover: https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/2023221-181339-pekopqxz71%E7%99%BE%E5%BA%A6%E6%94%B6%E5%BD%95.jpg
description: 对于github上搭建的hexo静态资源博客来说，因为在github上由于某种大家都知道的原因，百度对其博客内容的收录及其不友好，好在百度提供了主动推送功能
tags:
- Linux
- 工具
# 置顶优先级 数值越大优先级越高 #
sticky: 100070
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- hexo
- 百度收录
---

```shell script
#!/bin/bash  

# This is the basic bash script  
export LANG=zh_CN.UTF-8
request_date=`date`
request_log=/home/byzhao/baidu_log.log

echo "下载目录"

curl https://byzhao.cn/post-sitemap.xml -o /home/byzhao/output.xml

echo "解析文档....."

xpath -q -e '//url/loc/text()' /home/byzhao/output.xml | tee /home/byzhao/urls.txt

perl -p -i -e "s/endless-zby.github.io/www.byzhao.cn/g" /home/byzhao/urls.txt

echo "同步至百度收录平台....."

request_code=`curl -s -H 'Content-Type:text/plain' --data-binary @/home/byzhao/urls.txt "http://data.zz.baidu.com/urls?site=www.byzhao.cn&token=XXXXXXXXX" | jq`

echo "$request_code" >> $request_log

request_code2=`echo "$request_code" | grep success`

echo "$request_code2" >> $request_log

if [[ "$request_code2" == *success* ]]
    then
        request_message=`echo -e "$request_date \t 推送成功\n$request_code\n"`
        echo "$request_message" >> $request_log
        echo "$request_message"
        # 发送消息到手机
        remain=`echo $request_code | jq '.remain'`
        success_no=`echo $request_code | jq '.success'`
        echo "$remain" >> $request_log
        message=`curl -X "POST" "http://xxxx.xxxxxx.cn:xxxx/message/push" -H 'Content-Type: application/json;charset=utf-8' -d '{"text":"百度收录同步成功","desp":"### 百度收录同步成功  \n- 剩余推送数量：'$remain'  \n- 本地推送数量：'$success_no'","pushkey":"PDU1TtRhwbxSrMmJ38D4aPOduQdG82WcXOHVa"}' | jq`

        echo "$message"
    else
        request_message=`echo -e "$request_date \t 推送异常\n$request_code\n"`
        echo "$request_message" >> $request_log
        echo "$request_message"
fi
```
