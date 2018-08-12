---
layout:     post
title:      使用 oauth-client-starter 接入 OAuth 服务
date:       2018-08-12 18:11:50
summary:    oauth-client-starter usage
author:     Tneciv
categories: Java
thumbnail:  award
tags:
 - Spring Boot
 
---

## CMCCSI-OAUTH-CLIENT-SPRING-BOOT-STARTER

### 该 starter 为便于接入标准化平台 Oauth2 登录开发，只需在 spring boot 工程中引入并配置相关参数后即可使用

### 引入

在项目pom.xml文件中添加如下信息：
```xml
<project>

    <dependencies>
        <dependency>
            <groupId>com.tneciv.oauth</groupId>
            <artifactId>oauth-client-spring-boot-starter</artifactId>
            <version>1.0.0</version>
        </dependency>
    </repositories>
    
    <repositories>
        <repository>
            <id>maven-repo-master</id>
            <url>https://raw.githubusercontent.com/Tneciv/maven-repo/master/</url>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>always</updatePolicy>
            </snapshots>
        </repository>
    </repositories>

</project>
```

### 配置信息

在项目application.properties中添加如下信息
```
// 必填，true时可用
oauthclient.enabled=true
// 必填，Oauth服务提供clientId
oauthclient.clientId=oauth_client_id
// 必填，Oauth服务提供clientSeret
oauthclient.clientSecret=oauth_secret
// 必填，Oauth服务地址
oauthclient.oauthserverUrl=http://192.168.1.131:8083/
// 可选参数，callbackUri动态时可在调用方法处配置，详见方法调用
oauthclient.callbackUri=http://localhost:8082/oauth/callback/
```

### 方法调用

```java
@Controller
@RequestMapping("/oauth")
public class OAuthController {

    @Autowired
    private OauthClientTemplate clientTemplate;

    /**
     * 发起登录请求，重定向到Oauth服务器，完成登陆后执行callback
     *
     * @param response
     * @throws IOException
     */
    @GetMapping("/login")
    public void doOAuthLogin(HttpServletResponse response) throws IOException {
        // 需要动态设置 callbackUri 时调用
        this.clientTemplate.setCallbackUri("customcallbackuri");
        // 向 OauthServer 发起登录请求
        this.clientTemplate.redirectToOauthServer(response);
    }

    /**
     * 登录成功后处理 OauthServer 回调，获取 accessToken
     * 处理 callback 地址必须与发起登录时设置的 callbackUri 匹配
     *
     * @param authCode
     */
    @GetMapping("/callback")
    public void getCallbackCode(@RequestParam("code") String authCode) {
        // 使用动态 callbackUri 设置时调用
        TokenEntity tokenEntity = this.clientTemplate.getAccessToken(authCode, "customcallbackuri");

        TokenEntity token = this.clientTemplate.getAccessToken(authCode);
        String accessToken = token.getAccessToken();
    }

}
```