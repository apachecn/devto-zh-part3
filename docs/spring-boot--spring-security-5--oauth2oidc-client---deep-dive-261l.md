# Spring boot+Spring Security 5+oauth 2/OIDC 客户端-深潜

> 原文：<https://dev.to/shyamala_u/spring-boot--spring-security-5--oauth2oidc-client---deep-dive-261l>

在我之前的文章中，我们看到了用谷歌登录来保护你的应用程序是多么容易。

现在让我们来看看是什么组成了这一切。

# 登录流程

## 请求访问受保护端点，Google 认证流程开始

**先决条件**

1.  `application.yml`是否为[配置了客户端和提供商值的](https://dev.to/shyamala_u/spring-boot--spring-security-5--oauth2oidc-client---basics-4ibo#step-3)
2.  属性`spring.security.oauth2.client.registration.provider`中的提供者名称被设置为 google

当请求访问`http://localhost:8080/me`时，如果您只配置了一个身份提供者，Spring 会自动将您重定向到`http://localhost:8080/oauth2/authorization/google`。注册到 url 模式`/oauth2/authorization/*`的`OAuth2AuthorizationRequestRedirectFilter`将加载相应的配置并重定向到身份提供者。在我们的例子中是谷歌。

> 如果您希望您的用户在多个提供者之间进行选择，那么为多个提供者配置您的`application.yml`,但是您需要一个登录页面，在那里您可以有多个链接供用户选择。

## 验证成功后，谷歌重定向到应用的重定向 url

一旦用户成功通过 Google 认证，Google now 就会重定向到在 Google 开发者控制台中配置的应用重定向 url。在我们的例子中，我们选择了一个特定的 url `http://localhost:8080/login/oauth2/code/google`。这是因为 OAuth2 `OAuth2LoginAuthenticationFilter`的认证处理过滤器被注册为监听`/login/oauth2/code/*`。

oauth 2 loginauthenticationfilter 将认证委托给`OidcAuthorizationCodeAuthenticationProvider`，它做 3 件事:

*   用代码换令牌
*   验证 id _ 令牌
*   从 Google 众所周知的配置中，通过调用用户信息端点来填充用户信息

现在你可能会问，如果我注册了一个不同的重定向 URI，并希望 oauth 2 loginauthenticationfilter 来监听它会怎么样。这非常简单，你需要做的就是进行如下的安全配置

```
@Configuration
@EnableWebSecurity
class SecurityConfiguration: WebSecurityConfigurerAdapter() {
    override fun configure(http: HttpSecurity) {
        http
                .authorizeRequests()
                .anyRequest()
                .authenticated()
                .and()
                .oauth2Login()
                .redirectionEndpoint()
                .baseUri("/oauth/callback/*")

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来呢

作为成功认证的结果，您将获得一个类型为`OAuth2AuthenticationToken`的认证对象。这个令牌将包含来自`id_token`和`user info endpoint`的所有必要信息。

您可以通过
访问登录用户的所有数据

```
SecurityContextHolder.getContext().authentication as OAuth2AuthenticationToken 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
 @GetMapping("/me")
 fun hello(currentUser: OAuth2AuthenticationToken): ResponseEntity<OAuth2AuthenticationToken> {
        return ResponseEntity.ok(currentUser)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 但是访问和刷新令牌呢？

作为成功的 OpenID 连接流的结果，客户端应用接收三个令牌，`access_token, refresh_token and id_token`。我们可能想要使用这个访问令牌从资源服务器[访问一些受保护的资源，比如 google](https://developers.google.com/tasks/v1/reference/tasks/list) 的任务 API。`OAuth2AuthorizedClientService`跟踪与用户相关联的令牌。

```
 val currentUser = SecurityContextHolder.getContext().authentication as OAuth2AuthenticationToken
 val currentUserClientConfig = oAuth2AuthorizedClientService.loadAuthorizedClient(
                authorizedClientRegistrationId,
                currentUser.name)
  println("AccessToken: ${currentUserClientConfig.accessToken.tokenValue}")
  println("RefreshToken: ${currentUserClientConfig.refreshToken.tokenValue}") 
```

Enter fullscreen mode Exit fullscreen mode

## 但是访问令牌可以过期

当访问令牌过期时，资源服务器如[如 google](https://developers.google.com/tasks/v1/reference/tasks/list) 的任务 API 将返回 401 HTTP 状态，最简单的解决方案是抛出一个`OAuth2AuthorizationException`，它是一种`AuthenticationException`，将再次触发登录流。

但是我们也可以使用刷新令牌来自动刷新我们的令牌，方法是使用请求拦截器定制`RestTemplate`，该拦截器将在到期时刷新令牌

```
class BearerTokenInterceptor(private val oAuth2AuthorizedClientService: OAuth2AuthorizedClientService) : ClientHttpRequestInterceptor {

    companion object {
        val log: Logger = LoggerFactory.getLogger(BearerTokenInterceptor::class.java)
    }

    private var accessTokenExpiresSkew = Duration.ofMinutes(1)
    private val clock = Clock.systemUTC()

    override fun intercept(request: HttpRequest, body: ByteArray, execution: ClientHttpRequestExecution): ClientHttpResponse {
        val currentUser = SecurityContextHolder.getContext().authentication as OAuth2AuthenticationToken
        val currentUserClientConfig = currentUser.clientConfig()

        if (isExpired(accessToken = currentUserClientConfig.accessToken)) {
            log.info("AccessToken expired, refreshing automatically")
            refreshToken(currentUserClientConfig, currentUser)
        }

        request.headers[AUTHORIZATION] = "Bearer ${currentUserClientConfig.accessToken.tokenValue}"

        return execution.execute(request, body)
    }

    private fun OAuth2AuthenticationToken.clientConfig(): OAuth2AuthorizedClient {
        return oAuth2AuthorizedClientService.loadAuthorizedClient(
                authorizedClientRegistrationId,
                name) ?: throw CredentialsExpiredException("could not load client config for $name, reauthenticate")
    }

    private fun refreshToken(currentClient: OAuth2AuthorizedClient, currentUser: OAuth2AuthenticationToken) {
        val atr = refreshTokenClient(currentClient)
        if (atr == null || atr.accessToken == null) {
            log.info("Failed to refresh token for ${currentUser.name}")
            return
        }

        val refreshToken = atr.refreshToken ?: currentClient.refreshToken
        val updatedClient = OAuth2AuthorizedClient(
                currentClient.clientRegistration,
                currentClient.principalName,
                atr.accessToken,
                refreshToken
        )

        oAuth2AuthorizedClientService.saveAuthorizedClient(updatedClient, currentUser)
    }

    private fun refreshTokenClient(currentClient: OAuth2AuthorizedClient): OAuth2AccessTokenResponse? {

        val formParameters = LinkedMultiValueMap<String, String>()
        formParameters.add(OAuth2ParameterNames.GRANT_TYPE, AuthorizationGrantType.REFRESH_TOKEN.value)
        formParameters.add(OAuth2ParameterNames.REFRESH_TOKEN, currentClient.refreshToken?.tokenValue)
        formParameters.add(OAuth2ParameterNames.REDIRECT_URI, currentClient.clientRegistration.redirectUriTemplate)

        val requestEntity = RequestEntity
                .post(URI.create(currentClient.clientRegistration.providerDetails.tokenUri))
                .header(CONTENT_TYPE, APPLICATION_FORM_URLENCODED_VALUE)
                .body(formParameters)

        return try {
            val r = restTemplate(currentClient.clientRegistration.clientId, currentClient.clientRegistration.clientSecret)
            val responseEntity = r.exchange(requestEntity, OAuth2AccessTokenResponse::class.java)
            responseEntity.body
        } catch (e: OAuth2AuthorizationException) {
            log.error("Unable to refresh token ${e.error.errorCode}")
            throw OAuth2AuthenticationException(e.error, e)
        }
    }

    private fun isExpired(accessToken: OAuth2AccessToken): Boolean {
        val now = this.clock.instant()
        val expiresAt = accessToken.expiresAt ?: return false
        return now.isAfter(expiresAt.minus(this.accessTokenExpiresSkew))
    }

    private fun restTemplate(clientId: String, clientSecret: String): RestTemplate {
        return RestTemplateBuilder()
                .additionalMessageConverters(
                        FormHttpMessageConverter(),
                        OAuth2AccessTokenResponseHttpMessageConverter())
                .errorHandler(OAuth2ErrorResponseErrorHandler())
                .basicAuthentication(clientId, clientSecret)
                .build()
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

> 到目前为止，我还没有发现 oauth2-client 可以在用户会话中自动刷新令牌，请告诉我情况是否如此:)

# 结论

我试图把所有相关的部分放在一起，如果我错过了什么，请给我反馈:)