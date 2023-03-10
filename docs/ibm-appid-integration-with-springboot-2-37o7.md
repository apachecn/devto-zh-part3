# [ ð¬ð§ ] IBM AppId ä¸ Springboot 2 çéæ

> åæï¼<https://dev.to/bzinoun/ibm-appid-integration-with-springboot-2-37o7>

#### æä»¬è¦åä»ä¹

éè¿è¿ç¯æç« ï¼æä»¬å°å³æ³¨å¦ä½å° IBM Cloud æåºç OpenId Connect æå¡å¨ AppID ä¸ Springboot ä¸ç Java åºç¨ç¨åºéæã

#### åå³æ¡ä»¶

*   IBM äºå¸æ·:åè´¹çæ¬å°±è¶³å¤äº
*   å³äº OAuth2 æµç¨çç¥è¯
*   Java çæç³»ç»çç¥è¯ã

#### å è¡ IBM AppId

App ID æ¯æ¥èª IbmCloud ç OpenId Connect æå¡ãå®å¸®å©å¼åäººåç¨å è¡ä»£ç è½»æ¾å°å°èº«ä»½éªè¯æ·»å å°ä»ä»¬ç web æç§»å¨åºç¨ç¨åºä¸­ï¼å¹¶å¸®å©ä»ä»¬å¨ IBM Cloud ä¸ä¿æ¤ä»ä»¬çäºåçåºç¨ç¨åºåæå¡ãAppID å»ºè®®éè¿è¸ä¹¦è°·æ­ç­è¿æ¥...

App ID è¿æå©äºç®¡çç¨æ·ç¹å®çæ°æ®ï¼å¼åèå¯ä»¥ä½¿ç¨è¿äºæ°æ®æ¥æå»ºä¸ªæ§åçåºç¨ä½éªã

> /!\æä»¬åè®¾æ¨ç¥éå¦ä½åå»ºåºæ¬ç Java Spring Boot åºç¨ç¨åº

*å¥½äºï¼æä»¬æ¥å±ç¤ºä¸äºä»£ç å§ï¼*

#### ç¨ Spring Boot éç½® AppId

*   å°ä¾èµé¡¹æ·»å å° pom.xml

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

*   åå»º ResourceSecurityConfiguration Java ç±»ä»¥å¯ç¨ OAuth2 å Rest åè½ãæ­¤å¤ï¼ä» ResourceServerConfigurerAdapter ç±»è¿è¡æ©å±ï¼ä»¥ä¾¿ç¨åéç½®å®å¨è®¿é®ã

```
@Configuration
@RestController
@EnableResourceServer
public class ResourceSecurityConfiguration extends ResourceServerConfigurerAdapter {

} 
```

*   éè¿æ·»å  configure æ¹æ³æ¥è¦çå®å¨éç½®ã

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

*   å ResourceSecurityConfiguration Java ç±»æ·»å ä¸¤ä¸ªç«¯ç¹ï¼å½æ¨ä»åç«¯æ§è¡ GET è°ç¨æ¶ï¼å°å¯ä»¥è®¿é®è¿ä¸¤ä¸ªç«¯ç¹ã/user ç«¯ç¹ä¸ºæä»¬æä¾ç»å½çç¨æ·å¯¹è±¡(ä¸»ä½)ï¼/userInfo ç«¯ç¹è¿åä¸ä¸ªåå«è¯¦ç»ä¿¡æ¯çå­ç¬¦ä¸²ã

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

*   ä»æ¨å°ä»æå¡å­è¯/ IBM Cloud å¸æ·è·å¾çä¿¡æ¯ä¸­æ·»å å¿è¦ç AppId éç½®å¼å° application.properties è¦å®ææ¨çéç½®ï¼æ¨å¯ä»¥å¨ä»¥ä¸ä½ç½®æ¾å°è¿äºä¿¡æ¯:IBM Cloud å¸æ·->æå¡-> AppId ->åºç¨ç¨åº->æ·»å åºç¨ç¨åº(å¦æä»¥åæ²¡æ)->æ¥çå­è¯

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

1.  æåï¼ç»å½å¹¶æ³¨åï¼åè¿äºç«¯ç¹ååº HTTP è¯·æ±:
2.  ç­¾å°:`https://appid-oauth.eu-gb.bluemix.net/oauth/v3/{TenantId}/token`

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

*   æ³¨å:`https://appid-management.eu-gb.bluemix.net/management/v4/{TenantId}/cloud_directory/sign_up`

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

é£é½æ¯ä¹¡äº²ä»¬ï¼