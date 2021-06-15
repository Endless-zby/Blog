---
title: RabbitMQ消息队列实例测试
tags: []
id: '134'
categories:
  - - SpringBoot
date: 2019-04-20 18:35:37
---

RabbitMQ 即一个消息队列，主要是用来实现应用程序的异步和解耦，同时也能起到消息缓冲，消息分发的作用 。

```
具体的相关文字解释和技术详情看这篇文章：
https://www.cnblogs.com/dwlsxj/p/RabbitMQ.html
```

*   ### 这里用一个短信验证的实例来了解RabbitMQ
    

1.  ##### 先开通阿里云（其他运营商当然也行也行）的短信服务
    

登录阿里云，点击右上角的头像下拉菜单中的AccessKeys并创建自己的  
AccessKey ID 和 Access Key Secret

![](http://www.zby123.club/wp-content/uploads/2019/04/CFCKDEM1D4OF73UU8N-1-1024x182.png)

管理控制台 --> 产品与服务 --> 短信服务  
左侧的国内消息中添加“消息管理”和“模板管理”等待审核（审核过程也就两小时最多）

![](http://www.zby123.club/wp-content/uploads/2019/04/@M6OO_0VJ6O68Z0B3RS8-1024x198.png)

签名管理

![](http://www.zby123.club/wp-content/uploads/2019/04/N1VN7_389Z0_HY-1024x191.png)

模板管理

等待审核通过，先去看看阿里云提供的官方文档

[https://help.aliyun.com/document\_detail/55359.html?spm=5176.doc55284.6.581.m3Ro21](https://help.aliyun.com/document_detail/55359.html?spm=5176.doc55284.6.581.m3Ro21)

准备工作完成

*   代码部分

模块结构

![](http://www.zby123.club/wp-content/uploads/2019/04/9V06JHNOD013Q14HGFRE.png)

User模块负责校验验证码和数据库的写入  
Sms模块只负责从rabbitmq中获取消息并发送

先看User部分（数据库操作使用Jpa，所以这里entity和dao层省略）

pom.xml

```

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>MavenProject</artifactId>
        <groupId>com.zby</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <artifactId>MavenProject_User</artifactId>
    <dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
        <!-- mysql的jar -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <!--rabbitmq-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
        <!--redis-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <!-- 引入另一个工程 -->
        <dependency>
            <groupId>com.zby</groupId>
            <artifactId>MavenProject_Common</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
</project>
```

Service

```
package com.zby.user.service;
import com.zby.user.dao.UserDao;
import com.zby.user.entity.User;
import com.zby.util.IdWorker;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;
import java.util.Date;
import java.util.HashMap;
@Service
public class UserService {
    @Autowired
    private RedisTemplate redisTemplate;
    @Autowired
    private RabbitTemplate rabbitTemplate;
    @Autowired
    private UserDao userDao;
    @Autowired
    private IdWorker idWorker;
    //验证操作加入队列
    public void sendSms(String phone){
        //随机验证码
        String smsCode = ((int)(Math.random()*9000)+1000) + "";
        //储存redis
        System.out.println(smsCode);
        redisTemplate.opsForValue().set("sms_" + phone,smsCode);
        //数据整理
        HashMap<String, String> map = new HashMap<>();
        map.put("phone",phone);
        map.put("smscode",smsCode);
        //加入队列
        rabbitTemplate.convertAndSend("sms",map);
    }
    //模拟用户输入验证码并完成注册操作
    /*
    参数：
            User(username,password)
            smscode
     */
    public void addUser(User user, String smscode){
        //redis中获取以保存的验证码
        String redisCode = (String) redisTemplate.opsForValue().get("sms_" + user.getPhone());
        //数据校验
        if(!smscode.equals(redisCode)){
            throw new RuntimeException("验证码错误") ;
        }
        //初始化用户数据
        user.setId(idWorker.nextId() + "");
        user.setFans(0);
        user.setPassword(((int)(Math.random()*900000)+100000) + "");
        user.setRegisterTime(new Date());
        user.setLastLoginTime(new Date());
        user.setUpdateTime(new Date());
        userDao.save(user);
    }
}
```

Controller

```
package com.zby.user.controller;
import com.zby.entity.Result;
import com.zby.entity.StatusCode;
import com.zby.user.entity.User;
import com.zby.user.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
@RestController
@RequestMapping("UserController")
public class UserController {
    @Autowired
    private UserService userService;
    //验证操作加入队列
    @PostMapping("{phone}")
    public Result sendSms(@PathVariable String phone){
        userService.sendSms(phone);
        return new Result(true, StatusCode.OK,"加入rabbit队列成功",null);
    }
    //队列操作执行后保存用户数据
    @PostMapping("adduser/{smscode}")
    public Result addUser(@RequestBody User user,@PathVariable String smscode){
        userService.addUser(user,smscode);
        return new Result(true,StatusCode.OK,"注册成功",null);
    }
}
```

测试：（关于rabbitmq的安装使用在下一篇文章中讲解）

![](http://www.zby123.club/wp-content/uploads/2019/04/DCMOOEIWEABF7EOQVVV-1024x356.png)

可以看到已经有一条数据进入sms队列

![](http://www.zby123.club/wp-content/uploads/2019/04/H2M9HG@T1EW01WB86DQ.png)

打印看一下刚才的验证码

![](http://www.zby123.club/wp-content/uploads/2019/04/6X7XQKGUAZ0_F8R-1024x457.png)

![](http://www.zby123.club/wp-content/uploads/2019/04/KL8E2UKN7TIBLQCJ-1024x90.png)

数据库更新数据成功

现在开始处理消息队列

pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>MavenProject</artifactId>
        <groupId>com.zby</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <artifactId>MavenProject_Sms</artifactId>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
        <dependency>
            <groupId>com.aliyun</groupId>
            <artifactId>aliyun-java-sdk-core</artifactId>
            <version>4.1.0</version>
        </dependency>
        <dependency>
            <groupId>com.github.1991wangliang</groupId>
            <artifactId>aliyun-java-sdk-dysmsapi</artifactId>
            <version>1.0.0</version>
        </dependency>
    </dependencies>
</project>
```

application.yml

![](http://www.zby123.club/wp-content/uploads/2019/04/V5I3K4IDKOQWF@1L-1-1024x373.png)

SmsListener

```
package com.zby.sms.listener;
import com.aliyuncs.exceptions.ClientException;
import com.zby.sms.util.SmsUtil;
import org.springframework.amqp.rabbit.annotation.RabbitHandler;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
import java.util.Map;
@Component
@RabbitListener(queues = "sms")
public class SmsListener {
    @Autowired
    private SmsUtil smsUtil ;
    @Value("${aliyun.sms.templateCode}")
    private String templateCode ;
    @Value("${aliyun.sms.signName}")
    private String signName  ;
    @RabbitHandler
    public void sendSms(Map<String,String> map) throws ClientException {
        //先从MQ取
        String phone = map.get("phone") ;
        String smscode = map.get("smscode") ;
        String smscodeJsonStr = "{\"code\":\""+smscode+"\"}" ;
        System.out.println("手机号：" +phone);
        System.out.println("验证码：" +smscode);
        //发送
            smsUtil.sendSms(phone, templateCode, signName, smscodeJsonStr);
    }
}
```

SmsUtil

```
package com.zby.sms.util;
import com.aliyuncs.DefaultAcsClient;
import com.aliyuncs.IAcsClient;
import com.aliyuncs.dysmsapi.model.v20170525.SendSmsRequest;
import com.aliyuncs.exceptions.ClientException;
import com.aliyuncs.profile.DefaultProfile;
import com.aliyuncs.profile.IClientProfile;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;
@Component
public class SmsUtil {
    //产品名称:云通信短信API产品,开发者无需替换
    static final String product = "Dysmsapi";
    //产品域名,开发者无需替换
    static final String domain = "dysmsapi.aliyuncs.com";
    @Autowired
    private Environment env;
    public void sendSms(String mobile,String template_code,String sign_name,String param) throws ClientException {
        System.out.println(env);
        String accessKeyId = env.getProperty("aliyun.sms.accessKeyId");
        String accessKeySecret = env.getProperty("aliyun.sms.accessKeySecret");
        System.out.println(accessKeyId + "--" + accessKeySecret);
        //可自助调整超时时间
        System.setProperty("sun.net.client.defaultConnectTimeout", "10000");
        System.setProperty("sun.net.client.defaultReadTimeout", "10000");
        //初始化acsClient,暂不支持region化
        IClientProfile profile = DefaultProfile.getProfile("cn-hangzhou", accessKeyId, accessKeySecret);
        DefaultProfile.addEndpoint("cn-hangzhou", "cn-hangzhou", product, domain);
        IAcsClient acsClient = new DefaultAcsClient(profile);
        //组装请求对象-具体描述见控制台-文档部分内容
        SendSmsRequest request = new SendSmsRequest();
        //必填:待发送手机号
        request.setPhoneNumbers(mobile);
        //必填:短信签名-可在短信控制台中找到
        request.setSignName(sign_name);
        //必填:短信模板-可在短信控制台中找到
        request.setTemplateCode(template_code);
        //可选:模板中的变量替换JSON串,如模板内容为"亲爱的${name},您的验证码为${code}"时,此处的值为
        request.setTemplateParam(param);
        //选填-上行短信扩展码(无特殊需求用户请忽略此字段)
        //request.setSmsUpExtendCode("90997");
        //可选:outId为提供给业务方扩展字段,最终在短信回执消息中将此值带回给调用者
        request.setOutId("yourOutId");
        //hint 此处可能会抛出异常，注意catch
        acsClient.getAcsResponse(request);
    }
}
```

测试（启动后监听器自动从消息队列读取消息进行处理）

![](http://www.zby123.club/wp-content/uploads/2019/04/D9HTQ_E69M7N8ARC6-1024x207.png)

![](http://www.zby123.club/wp-content/uploads/2019/04/BXKF41J7O7L8XPLO1Y-1024x321.png)

可以看到刚才队列中的数据已经被处理，当前队列中的准备数据为0

![](http://www.zby123.club/wp-content/uploads/2019/04/BVW496T6@ZIIINY4.png)