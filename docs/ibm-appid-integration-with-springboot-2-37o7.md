# [ 🇬🇧 ] IBM AppId 与 Springboot 2 的集成

> 原文：<https://dev.to/bzinoun/ibm-appid-integration-with-springboot-2-37o7>

#### 我们要做什么

通过这篇文章，我们将关注如何将 IBM Cloud 提出的 OpenId Connect 服务器 AppID 与 Springboot 下的 Java 应用程序集成。

#### 先决条件

*   IBM 云帐户:免费版本就足够了
*   关于 OAuth2 流程的知识
*   Java 生态系统的知识。

#### 几行 IBM AppId

App ID 是来自 IbmCloud 的 OpenId Connect 服务。它帮助开发人员用几行代码轻松地将身份验证添加到他们的 web 或移动应用程序中，并帮助他们在 IBM Cloud 上保护他们的云原生应用程序和服务。AppID 建议通过脸书谷歌等连接...

App ID 还有助于管理用户特定的数据，开发者可以使用这些数据来构建个性化的应用体验。

> /!\我们假设您知道如何创建基本的 Java Spring Boot 应用程序

*好了，我们来展示一些代码吧！*

#### 用 Spring Boot 配置 AppId

*   将依赖项添加到 pom.xml

```
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <!-- spring-security-oauth2-autoconfigure tells the framework to use OAuth2, instead of basic http security -->
        <dependency>
            <groupId>org.springframework.security.oauth.boot</groupId>
            <artifactId>spring-security-oauth2-autoconfigure</artifactId>
            <version>2.0.0.RELEASE</version>
        </dependency> 
```

*   创建 ResourceSecurityConfiguration Java 类以启用 OAuth2 和 Rest 功能。此外，从 ResourceServerConfigurerAdapter 类进行扩展，以便稍后配置安全访问。

```
@Configuration
@RestController
@EnableResourceServer
public class ResourceSecurityConfiguration extends ResourceServerConfigurerAdapter {

} 
```

*   通过添加 configure 方法来覆盖安全配置。

```
@Configuration
@RestController
@EnableResourceServer
public class ResourceSecurityConfiguration extends ResourceServerConfigurerAdapter {
    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
        .antMatchers("/api**", "/user", "/userInfo").authenticated()
        .and().logout().logoutSuccessUrl("/").permitAll()
        .and().csrf().disable();
    }
} 
```

*   向 ResourceSecurityConfiguration Java 类添加两个端点，当您从前端执行 GET 调用时，将可以访问这两个端点。/user 端点为我们提供登录的用户对象(主体)，/userInfo 端点返回一个包含详细信息的字符串。

```
@Configuration
@RestController
@EnableResourceServer
public class ResourceSecurityConfiguration extends ResourceServerConfigurerAdapter {
    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
        .antMatchers("/api**", "/user", "/userInfo").authenticated()
        .and().logout().logoutSuccessUrl("/").permitAll()
        .and().csrf().disable();
    }
    // for open id
    @RequestMapping("/user")
    public Principal user(Principal principal) {
        //Principal holds the logged in user information.
        // Spring automatically populates this principal object after login.
        return principal;
    }

    // for open id
    @RequestMapping("/userInfo")
    public String userInfo(Principal principal){
        final OAuth2Authentication oAuth2Authentication = (OAuth2Authentication) principal;
        final Authentication authentication = oAuth2Authentication.getUserAuthentication();
        //Manually getting the details from the authentication, and returning them as String.
        return authentication.getDetails().toString();
    }

} 
```

*   从您将从服务凭证/ IBM Cloud 帐户获得的信息中添加必要的 AppId 配置值到 application.properties 要完成您的配置，您可以在以下位置找到这些信息:IBM Cloud 帐户->服务-> AppId ->应用程序->添加应用程序(如果以前没有)->查看凭证

```
spring.main.allow-bean-definition-overriding=true

env.urlAppid= // OAuth server URL from IBM AppID account

# SECURITY OAUTH2 CLIENT (OAuth2ClientProperties)

security.oauth2.client.accessTokenUri=${env.urlAppid}/token
security.oauth2.client.userAuthorizationUri=${env.urlAppid}/authorization

# SECURITY OAUTH2 RESOURCES (ResourceServerProperties)
security.oauth2.resource.userInfoUri=${env.urlAppid}/userinfo

## Setting to change in case of changing app id dev instance
security.oauth2.client.client-id= // IBM AppId account client-id
security.oauth2.client.client-secret= // IBM AppId secret 
```

1.  最后，登录并注册，向这些端点发出 HTTP 请求:
2.  签到:`https://appid-oauth.eu-gb.bluemix.net/oauth/v3/{TenantId}/token`

```
Request: {
username: '',
password: '',
grant_type: 'password' // don't change
}
RequestHeader:{
Authorization: 'Basic ClientId:Secret'
}

Response:{
access_token:'',
} 
```

*   注册:`https://appid-management.eu-gb.bluemix.net/management/v4/{TenantId}/cloud_directory/sign_up`

```
Request:{
name: '',
username: '',
password: '',
active: true, // don't change
emails: [
{
value: 'email@example.com',
primary: true
}
]
}

RequestHeader:{
   Authorization: 'Bearer {IAM_TOKEN}'
}

Response:{
HttpStatus: 200
} 
```

那都是乡亲们！