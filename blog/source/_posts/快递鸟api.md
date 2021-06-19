---
title: 快递鸟API
updated: 2021-06-19 21:16:33
description: 自己整一个查快递的小玩意
tags:
- Java
cover: https://i.loli.net/2021/06/19/7hmAqYdXv6xPlDj.jpg
# 置顶优先级 数值越大优先级越高 #
sticky: 9980
# 【可选】文章分类 #
categories: Java
# 【可选】文章关键字 #
keywords:
- 快递
date: 2019-09-26 10:13:27
---

#### api申请

{% btn 'http://www.kdniao.com/',去官网申请开发者api,far fa-hand-point-right,green larger %}


![](https://i.loli.net/2021/06/19/QwpHA9L2Nefbv1t.png)

> `免费版`提供的业务有如下：

![](https://i.loli.net/2021/06/19/WZfKsRJnG95PV21.png)

> 这里我只以`物流跟踪`和`单号识别`来举例子（这两个最基础最常用）

#### API规范

> 以`物流跟踪`为例：

请求：

![](https://i.loli.net/2021/06/19/lKNLkc9CpuA3f61.png)

__需要注意的是DataSign参数的编码！__

>接口需要指定快递单号的快递公司编码，格式不对或则编码错误都会返失败的信息。 参照如下表！

{% btn 'http://www.kdniao.com/file/%E5%BF%AB%E9%80%92%E9%B8%9F%E6%8E%A5%E5%8F%A3%E6%94%AF%E6%8C%81%E5%BF%AB%E9%80%92%E5%85%AC%E5%8F%B8%E7%BC%96%E7%A0%81.xlsx',查看快递公司编码,far fa-hand-point-right,blue larger %}

- yml配置：

![](https://i.loli.net/2021/06/19/VL6FZkUwvPBeuqf.png)

- Entity：

![](https://i.loli.net/2021/06/19/Ppwsr4aquG3AbTh.png)


```java
package club.zby.express.Controller;

import club.zby.express.Config.Result;
import club.zby.express.Config.StatusCode;
import club.zby.express.Entity.Express;
import club.zby.express.Untlis.ExpressUntlis;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiImplicitParam;
import io.swagger.annotations.ApiImplicitParams;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.HashMap;
import java.util.Map;

@RestController
@RequestMapping(value = "Express")
@Api(value = "物流追踪接口")
public class ExpressController {


    @Autowired
    private ExpressUntlis expressUntlis;
    @Autowired
    private Express express;


    @ApiOperation(value = "根据物流单号和快递公司编码查询物流信息", notes = "根据物流单号和快递公司编码查询物流信息", httpMethod = "GET")
    @ApiImplicitParams({
            @ApiImplicitParam(paramType="query",name = "shipChannel",required = true, value = "快递公司编码",dataType = "String"),
            @ApiImplicitParam(paramType="query",name = "shipSn",required = true, value = "物流单号", dataType = "String" )
    })
    @ResponseBody
    @GetMapping("getOrderTracesByJson")
    public Result getOrderTracesByJson(@RequestParam("shipChannel") String shipChannel, @RequestParam("shipSn") String shipSn) throws Exception{
        String requestData= "{'OrderCode':'','ShipperCode':'" + shipChannel + "','LogisticCode':'" + shipSn + "'}";
        System.out.println("请求报文：" + requestData);
        Map<String, String> params = new HashMap<String, String>();
        params.put("RequestData", expressUntlis.urlEncoder(requestData, "UTF-8")); //请求内容需进行URL(utf-8)编码。请求内容JSON格式，须和DataType一致。
        params.put("EBusinessID", express.getEBusinessID());   //商户ID，请在我的服务页面查看。
        params.put("RequestType", "1002");  //请求指令类型：即时查询API:1002
        String dataSign=expressUntlis.encrypt(requestData, express.getAppKey(), "UTF-8");    //数据内容签名：把(请求内容(未编码)+AppKey)进行MD5加密，然后Base64编码，最后 进行URL(utf-8)编码。
        params.put("DataSign", expressUntlis.urlEncoder(dataSign, "UTF-8"));
        params.put("DataType", "2");    //请求、返回数据类型：2-json；

        String result=expressUntlis.sendPost(express.getReqURL(), params);
        System.out.println("返回报文：" + result);
        //根据公司业务处理返回的信息......
        return new Result(true, StatusCode.OK,"查询结果集",result);
    }


    @ApiOperation(value = "根据物流单号查询快递公司编码", notes = "根据物流单号查询快递公司编码", httpMethod = "GET")
    @ApiImplicitParams({
            @ApiImplicitParam(paramType="query",name = "shipSn",required = true, value = "物流单号", dataType = "String" )
    })
    @ResponseBody
    @GetMapping("/getOrderShipperCode")
    public Result getOrderShipperCode(@RequestParam("shipSn") String shipSn) throws Exception{
        String requestData= "{'LogisticCode':'" + shipSn + "'}";
        System.out.println("请求报文：" + requestData);
        Map<String, String> params = new HashMap<String, String>();
        params.put("RequestData", expressUntlis.urlEncoder(requestData, "UTF-8"));
        params.put("EBusinessID", express.getEBusinessID());
        params.put("RequestType", "2002");
        String dataSign=expressUntlis.encrypt(requestData, express.getAppKey(), "UTF-8");
        params.put("DataSign", expressUntlis.urlEncoder(dataSign, "UTF-8"));
        params.put("DataType", "2");

        String result=expressUntlis.sendPost(express.getReqURL(), params);
        //根据公司业务处理返回的信息......
        return new Result(true, StatusCode.OK,"查询结果集",result);
    }
}
```

- 测试结果：

![](https://i.loli.net/2021/06/19/kQH3olnzDZ6vaEy.png)

![](https://i.loli.net/2021/06/19/zTUgxv9bh8iLka3.png)

![](https://i.loli.net/2021/06/19/S4LPhnZw6bQJ29T.png)

- 对返回结果进行格式化

![](https://i.loli.net/2021/06/19/YHUlxmu63c4PFvX.png)