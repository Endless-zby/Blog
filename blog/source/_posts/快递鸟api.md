---
title: 快递鸟API
tags: []
id: '1239'
categories:
  - - 小玩意
  - - 工具
date: 2019-09-26 10:13:27
---

#### api申请

地址： [http://www.kdniao.com/](http://www.kdniao.com/)

![](https://www.zby123.club/wp-content/uploads/2019/09/kuaidi1-1024x262.png)

免费版提供的业务有如下：

![](https://www.zby123.club/wp-content/uploads/2019/09/kuaidi2-1024x599.png)

这里我只以物流跟踪和单号识别来举例子（这两个最基础最常用）

#### API规范

以物流跟踪为例：

请求：

![](https://www.zby123.club/wp-content/uploads/2019/09/kuaidi4-1024x331.png)

需要注意的是DataSign参数的编码！

接口需要指定快递单号的快递公司编码，格式不对或则编码错误都会返失败的信息。 参照如下表！

[2019快递鸟接口支持快递公司编码](https://www.zby123.club/wp-content/uploads/2019/09/2019快递鸟接口支持快递公司编码.xlsx)[下载](https://www.zby123.club/wp-content/uploads/2019/09/2019快递鸟接口支持快递公司编码.xlsx)

yml配置：

![](https://www.zby123.club/wp-content/uploads/2019/09/MPM8A_8P4LIKU_GBVHBDI6.png)

Entity：

![](https://www.zby123.club/wp-content/uploads/2019/09/kuaidi6-1024x629.png)

使用@Configuration注解读取yml，注入

本地使用Swagger2来测试，直接上代码

```
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

测试：

![](https://www.zby123.club/wp-content/uploads/2019/09/kuaidi7-1024x556.png)

![](https://www.zby123.club/wp-content/uploads/2019/09/kuaidi8-1024x556.png)

![](https://www.zby123.club/wp-content/uploads/2019/09/kuaidi9-1024x245.png)

对返回结果进行格式化

![](https://www.zby123.club/wp-content/uploads/2019/09/kuaidi10-1024x466.png)