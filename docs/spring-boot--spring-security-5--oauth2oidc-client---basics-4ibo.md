# Spring boot+Spring Security 5+oauth 2/OIDC 客户端-基础

> 原文：<https://dev.to/shyamala_u/spring-boot--spring-security-5--oauth2oidc-client---basics-4ibo>

由于很久以来我就想用 Spring Security 集成一个 OpenID Connect 提供者，最后一次尝试的时候，我觉得很复杂，就自己写了[库](https://github.com/shyamz-22/openid-connect-client)。由于 Spring Security 5 具有对 OAuth2 客户端的原生支持，并扩展了它对 OpenID connect 的使用，我想看看它有多容易集成。

对于这个例子，我们将构建一个简单的应用程序，当我们试图访问一个受保护的端点时，它会重定向到 google

# 第一步:

从 [https://start.spring.io](https://start.spring.io) 创建一个 spring boot 项目，具有以下依赖关系

```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-oauth2-client'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'com.fasterxml.jackson.module:jackson-module-kotlin'
    implementation 'org.jetbrains.kotlin:kotlin-reflect'
    implementation 'org.jetbrains.kotlin:kotlin-stdlib-jdk8'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.security:spring-security-test'
} 
```

# 第二步:

创建一个将显示当前用户认证数据的端点

```
@RestController
class HelloController {

    @GetMapping("/me")
    fun hello(currentUser: OAuth2AuthenticationToken): ResponseEntity<OAuth2AuthenticationToken> {
        return ResponseEntity.ok(currentUser)
    }

} 
```

# 第三步:

在 application.yml 中配置 OAuth2 客户端信息，在 google 的开发者控制台中将应用的重定向 uri 配置为`http://localhost:8080/login/oauth2/code/google`

```
# @see https://console.developers.google.com/apis/ to create your client credentials
logging.level.org.springframework: INFO
spring:
  security:
    oauth2:
      client:
        registration:
          google:
            provider: google
            client-id: <<your-client-id>>
            client-secret: <<your-client-secret>> 
            client-authentication-method: basic
            authorization-grant-type: authorization_code
            scope:
              - openid
              - email
              - profile
              - https://www.googleapis.com/auth/tasks.readonly
        provider:
          google:
            issuer-uri: https://accounts.google.com 
```

# 第四步:

运行应用程序，转到 [http://localhost:8080/me](http://localhost:8080/me) ，完成登录过程，您会看到这个。

```
{  "authorities":  [  {  "authority":  "ROLE_USER",  "attributes":  {  "at_hash":  "28AV0o6xKM8f3UQlljlGuw",  "sub":  "10080000000000000",  "email_verified":  true,  "iss":  "https://accounts.google.com",  "given_name":  "Syamala",  "locale":  "en",  "picture":  "https://lh6.googleusercontent.com/photo.jpg",  "aud":  [  "client-id"  ],  "azp":  "client-id",  "name":  "Syamala Umamaheswaran",  "exp":  "2019-03-24T18:27:19Z",  "family_name":  "Umamaheswaran",  "iat":  "2019-03-24T17:27:19Z",  "email":  "xxxx@gmail.com"  },  "idToken":  {...},  "userInfo":  null  }  ],  "details":  null,  "authenticated":  true,  "principal":  {},  "authorizedClientRegistrationId":  "google",  "credentials":  "",  "name":  "10080000000000000"  } 
```

# 心神俱碎:

[![Mind Blown](img/856db027d08ace81a9ea52b68c324944.png)](https://i.giphy.com/media/3ohc0Xk1JCH35wvrVu/giphy.gif)

让我震惊的是，无需编写任何安全代码，我们就可以与 OpenID Connect 提供者集成，我需要知道这是如何如此容易地实现的。细节决定成败，敬请关注我的下一篇博文，我将在其中解释幕后操作，以及如何访问受保护的资源和如何自动刷新令牌。

完整源代码@[https://github.com/shyamz-22/oidc-spring-security-5](https://github.com/shyamz-22/oidc-spring-security-5)