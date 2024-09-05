---
layout: posts
title: 自动更新GitHubHosts
date: 2024-07-23 14:55:08
updated: 2024-07-23 14:55:08
top_img: https://i.loli.net/2021/06/19/H8CJOieu5nhE7mZ.jpg
cover: https://i.loli.net/2021/06/19/OgjM2SmRXipBwlC.jpg
description: 定时更新hosts，解决无法访问GitHub问题
tags:
- Linux
- 工具
# 置顶优先级 数值越大优先级越高 #
sticky: 100250
# 【可选】文章分类 #
categories: 工具
# 【可选】文章关键字 #
keywords:
- GitHub
- hosts
---
{% note orange 'fas fa-battery-half' flat %}
此方法并不能适用于SNI阻断场景
{% endnote %}

```shell
#!/bin/bash  
# byzhao
# This is the basic bash script  
export LANG=zh_CN.UTF-8
request_log=/home/byzhao/github_hosts.log
download_file=/etc/hosts.download

sudo curl -s -o "$download_file" https://raw.hellogithub.com/hosts >/dev/null 2>&1  
if [ $? -eq 0 ]; then  
        echo "文件下载成功....."
        start_num=$(awk '/GitHub520 Host Start/ {print NR; exit}' /etc/hosts)
        end_num=$(awk '/GitHub520 Host End/ {last_match_line=NR} END {print last_match_line}' /etc/hosts)

        if [ -n "${start_num}" ] && [ -n "${end_num}"  ]; then
                echo "解析文件行: ${start_num}, ${end_num}"

                # 删除对应行  
                if sudo sed -i "${start_num},${end_num}d" /etc/hosts; then  
                        echo "删除原github hosts"  
                else  
                        echo "删除原github hosts失败"  
                        exit 1  
                fi
        fi
        # 追加新的hosts到/etc/hosts
        if sudo sh -c "cat $download_file >> /etc/hosts"; then  
            echo "追加新的hosts"  
        else  
            echo "追加新的hosts失败"  
            exit 1  
        fi  

        sudo rm -f "{$download_file}"
        echo "删除临时文件"
        curl -X "POST" "http://xxx.xxx.cn:8081/wuaFrBQHBvF25bbPEt69Mg" -H 'Content-Type: application/json; charset=utf-8' -d '{"title": "GitHub","body": "hosts更新完成","group": "GitHub"}'
        echo "完成..."
else  
  echo "下载失败"  
fi
```

![202495-175250-vbr2cfn3dg1725529970083.png](https://byzhao-blog-1257201044.cos.ap-beijing.myqcloud.com/blog/202495-175250-vbr2cfn3dg1725529970083.png)
