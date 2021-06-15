---
title: QQ第三方登录
tags: []
id: '1230'
categories:
  - - 小玩意
  - - 工具
date: 2019-09-26 09:45:57
---

*   QQ互联平台上申请对接（根据自己开发选择应用环境）

![](https://www.zby123.club/wp-content/uploads/2019/09/qqhulian--1024x529.png)

申请过程中审核时间比较长

![](https://www.zby123.club/wp-content/uploads/2019/09/qqhulian-1-1024x233.png)

![](https://www.zby123.club/wp-content/uploads/2019/09/qqhulian-2-1024x607.png)

回调地址可以理解为提供给腾讯一个你的服务地址，让腾讯调用并返回你需要的内容  
回调地址不能是本地地址，所以我这里使用了内网穿透方便在本地测试

*   稍微看一下接口调用的注意点

对于应用而言，使用qq的注册信息只需要两步

1、获取Authorization Code；  
2、通过Authorization Code获取Access Token

先看一下第一步 获取Authorization Code

**请求地址**：  
PC网站：https://graph.qq.com/oauth2.0/authorize  
**请求方法**：  
GET  
**请求参数**：![](http://www.zby123.club/wp-content/uploads/2019/09/qqhulian-3.png)

**返回：**

如果用户成功登录并授权，则会跳转到指定的回调地址，并在redirect\_uri地址后带上Authorization Code和原始的state值。如：

PC网站：http://graph.qq.com/demo/index.jsp?code=9A5F\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*06AF&state=test

第二步，通过Authorization Code获取Access Token

**请求地址**：  
PC网站：https://graph.qq.com/oauth2.0/token  
**请求方法**：  
GET  
**请求参数**： ![](http://www.zby123.club/wp-content/uploads/2019/09/qqhulian-4.png)

**返回：**

![](https://www.zby123.club/wp-content/uploads/2019/09/qqhulian-5.png)

*   直接上代码吧

```
package club.zby.newplan.service;

import club.zby.newplan.Entity.Constants;
import club.zby.newplan.Entity.QQUserInfo;
import club.zby.newplan.Untlis.HttpClientUtils;
import club.zby.newplan.Untlis.URLEncodeUtil;
import com.alibaba.fastjson.JSON;
import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.bind.annotation.GetMapping;

import java.util.HashMap;
import java.util.Map;

@Service
public class QQService  {

    @Autowired
    private Constants constants;

    /**
     * 获取链接
     * @return
     * @throws Exception
     */
    public String getCode(){
        // 拼接url
        StringBuilder url = new StringBuilder();
        url.append("https://graph.qq.com/oauth2.0/authorize?");
        url.append("response_type=code");
        url.append("&client_id=" + constants.getQqAppId());
        // 回调地址 ,回调地址要进行Encode转码
        String redirect_uri = constants.getQqRedirectUrl();
        // 转码
        url.append("&redirect_uri=" + URLEncodeUtil.getURLEncoderString(redirect_uri));
        url.append("&state=ok");
        // HttpClientUtils.get(url.toString(), "UTF-8");
        System.out.println(url.toString());
        return url.toString();
    }

    /**
     * 获得token信息（授权，每个用户的都不一致） --> 获得token信息该步骤返回的token期限为一个月
     * (保存到Map<String,String> qqProperties)
     * @param code
     * @return
     * @throws Exception
     */
    public Map<String, Object> getToken(String code) throws Exception {
        StringBuilder url = new StringBuilder();
        url.append("https://graph.qq.com/oauth2.0/token?");
        url.append("grant_type=authorization_code");

        url.append("&client_id=" + constants.getQqAppId());
        url.append("&client_secret=" + constants.getQqAppSecret());
        url.append("&code=" + code);
        // 回调地址
        String redirect_uri = constants.getQqRedirectUrl();
        // 转码
        url.append("&redirect_uri=" + URLEncodeUtil.getURLEncoderString(redirect_uri));
        // 获得token
        String result = HttpClientUtils.get(url.toString(), "UTF-8");
        System.out.println("url:" + url.toString());
        // 把token保存
        String[] items = StringUtils.splitByWholeSeparatorPreserveAllTokens(result, "&");
        String accessToken = StringUtils.substringAfterLast(items[0], "=");
        Long expiresIn = new Long(StringUtils.substringAfterLast(items[1], "="));
        String refreshToken = StringUtils.substringAfterLast(items[2], "=");
        //token信息
        Map<String,Object > qqProperties = new HashMap<String,Object >();
        qqProperties.put("accessToken", accessToken);
        qqProperties.put("expiresIn", String.valueOf(expiresIn));
        qqProperties.put("refreshToken", refreshToken);
        return qqProperties;
    }

    /**
     * 刷新token 信息（token过期，重新授权）
     *
     * @return
     * @throws Exception
     */
    @GetMapping("/refreshToken")
    public Map<String,Object> refreshToken(Map<String,Object> qqProperties) throws Exception {
        // 获取refreshToken
        String refreshToken = (String) qqProperties.get("refreshToken");
        StringBuilder url = new StringBuilder("https://graph.qq.com/oauth2.0/token?");
        url.append("grant_type=refresh_token");
        url.append("&client_id=" + constants.getQqAppId());
        url.append("&client_secret=" + constants.getQqAppSecret());
        url.append("&refresh_token=" + refreshToken);
        System.out.println("url:" + url.toString());
        String result = HttpClientUtils.get(url.toString(), "UTF-8");
        // 把新获取的token存到map中
        String[] items = StringUtils.splitByWholeSeparatorPreserveAllTokens(result, "&");
        String accessToken = StringUtils.substringAfterLast(items[0], "=");
        Long expiresIn = new Long(StringUtils.substringAfterLast(items[1], "="));
        String newRefreshToken = StringUtils.substringAfterLast(items[2], "=");
        //重置信息
        qqProperties.put("accessToken", accessToken);
        qqProperties.put("expiresIn", String.valueOf(expiresIn));
        qqProperties.put("refreshToken", newRefreshToken);
        return qqProperties;
    }

    /**
     * 获取用户openId（根据token）
     * 把openId存到map中
     * @param
     * @return
     * @throws Exception
     */
    public String getOpenId(Map<String,Object> qqProperties) throws Exception {
        // 获取保存的用户的token
        String accessToken = (String) qqProperties.get("accessToken");
        if (!StringUtils.isNotEmpty(accessToken)) {
            // return "未授权";
        }
        StringBuilder url = new StringBuilder("https://graph.qq.com/oauth2.0/me?");
        url.append("access_token=" + accessToken);
        String result = HttpClientUtils.get(url.toString(), "UTF-8");
        String openId = StringUtils.substringBetween(result, "\"openid\":\"", "\"}");
        return openId;
    }

    /**
     * 根据token,openId获取用户信息
     */
    public QQUserInfo getUserInfo(String openid,Map<String,Object> qqProperties) throws Exception {
        // 取token
        String accessToken = (String) qqProperties.get("accessToken");
        if (!StringUtils.isNotEmpty(accessToken)  !StringUtils.isNotEmpty(openid)) {
            return null;
        }
        //拼接url
        StringBuilder url = new StringBuilder("https://graph.qq.com/user/get_user_info?");
        url.append("access_token=" + accessToken);
        url.append("&oauth_consumer_key=" + constants.getQqAppId());
        url.append("&openid=" + openid);
        // 获取qq相关数据
        String result = HttpClientUtils.get(url.toString(), "UTF-8");
        Object json = JSON.parseObject(result, QQUserInfo.class);
        QQUserInfo userInfo = (QQUserInfo) json;
        return userInfo;
    }

}
```