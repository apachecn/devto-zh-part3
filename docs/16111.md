# JWT 的春天安全

> 原文：<https://dev.to/kubadlo/spring-security-with-jwt-3j76>

Spring Security 的默认行为很容易用于标准的 web 应用程序。它使用基于 cookie 的身份验证和会话。此外，它会自动为您处理 CSRF 令牌(以防止中间人攻击)。在大多数情况下，您只需要为特定的路由设置授权权限，一种从数据库中检索用户的方法，仅此而已。

另一方面，如果您只是构建一个 REST API，供外部服务或您的 SPA/移动应用程序使用，您可能不需要完整会话。JWT (JSON Web 令牌)来了——一个小型的数字签名令牌。所有需要的信息都可以存储在令牌中，因此您的服务器可以是无会话的。

JWT 需要附加到每个 HTTP 请求上，这样服务器才能授权您的用户。有一些关于如何发送令牌的选项。例如，作为 URL 参数或在 HTTP 授权头中使用承载模式:

```
Authorization: Bearer <token string> 
```

Enter fullscreen mode Exit fullscreen mode

JSON Web Token 包含三个主要部分:

1.  标头—通常包括令牌类型和哈希算法。
2.  有效负载—通常包括关于用户和令牌发放对象的数据。
3.  签名——用于验证消息是否在传输过程中未被更改。

## 示例令牌

授权头中的 JWT 令牌可能如下所示:

```
Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJpc3MiOiJzZWN1cmUtYXBpIiwiYXVkIjoic2VjdXJlLWFwcCIsInN1YiI6InVzZXIiLCJleHAiOjE1NDgyNDI1ODksInJvbCI6WyJST0xFX1VTRVIiXX0.GzUPUWStRofrWI9Ctfv2h-XofGZwcOog9swtuqg1vSkA8kDWLcY3InVgmct7rq4ZU3lxI6CGupNgSazypHoFOA 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，有三个部分用逗号分隔——标题、声明和签名。头和负载是 Base64 编码的 JSON 对象。

#### 表头:

```
{  "typ":  "JWT",  "alg":  "HS512"  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 索赔/有效载荷:

```
{  "iss":  "secure-api",  "aud":  "secure-app",  "sub":  "user",  "exp":  1548242589,  "rol":  [  "ROLE_USER"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

## 示例应用

在下面的例子中，我们将创建一个简单的 API，它有两个路由——一个对公众开放，另一个只对授权用户开放。

我们将使用 page [start.spring.io](https://start.spring.io/) 来创建我们的应用程序框架，并选择安全性和 Web 依赖项。其余的选项取决于您的偏好。

[![Spring Initializr screenshot](img/0fcdf50bda679b715af20ab9e9118f30.png "Screenshot of Spring Initializr with new application details")](https://res.cloudinary.com/practicaldev/image/fetch/s--vJn5Oert--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jakublesko.cimg/blog/2019-01-14-spring-initializr.png)

Java 的 JWT 支持是由库 [JJWT](https://github.com/jwtk/jjwt) 提供的，所以我们还需要向 pom.xml 文件添加以下依赖项:

```
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.10.5</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.10.5</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.10.5</version>
    <scope>runtime</scope>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

## 控制器

我们的示例应用程序中的控制器将尽可能简单。如果用户未被授权，它们将返回一条消息或 HTTP 403 错误代码。

```
@RestController
@RequestMapping("/api/public")
public class PublicController {

    @GetMapping
    public String getMessage() {
        return "Hello from public API controller";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
@RestController
@RequestMapping("/api/private")
public class PrivateController {

    @GetMapping
    public String getMessage() {
        return "Hello from private API controller";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 滤镜

首先，我们将为 jwt 的生成和验证定义一些可重用的常量和默认值。

*注意:您不应该将 JWT 签名密钥硬编码到您的应用程序代码中(在本例中我们暂时忽略这一点)。您应该使用环境变量或。属性文件。此外，密钥需要有适当的长度。例如，HS512 算法需要大小至少为 512 字节的密钥。*

```
public final class SecurityConstants {

    public static final String AUTH_LOGIN_URL = "/api/authenticate";

    // Signing key for HS512 algorithm
    // You can use the page http://www.allkeysgenerator.com/ to generate all kinds of keys
    public static final String JWT_SECRET = "n2r5u8x/A%D*G-KaPdSgVkYp3s6v9y$B&E(H+MbQeThWmZq4t7w!z%C*F-J@NcRf";

    // JWT token defaults
    public static final String TOKEN_HEADER = "Authorization";
    public static final String TOKEN_PREFIX = "Bearer ";
    public static final String TOKEN_TYPE = "JWT";
    public static final String TOKEN_ISSUER = "secure-api";
    public static final String TOKEN_AUDIENCE = "secure-app";

    private SecurityConstants() {
        throw new IllegalStateException("Cannot create instance of static util class");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第一个过滤器将直接用于用户验证。它将检查来自 URL 的用户名和密码参数，并调用 Spring 的认证管理器来验证它们。

如果用户名和密码是正确的，那么过滤器将创建一个 JWT 令牌并在 HTTP 授权头中返回它。

```
public class JwtAuthenticationFilter extends UsernamePasswordAuthenticationFilter {

    private final AuthenticationManager authenticationManager;

    public JwtAuthenticationFilter(AuthenticationManager authenticationManager) {
        this.authenticationManager = authenticationManager;

        setFilterProcessesUrl(SecurityConstants.AUTH_LOGIN_URL);
    }

    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) {
        var username = request.getParameter("username");
        var password = request.getParameter("password");
        var authenticationToken = new UsernamePasswordAuthenticationToken(username, password);

        return authenticationManager.authenticate(authenticationToken);
    }

    @Override
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response,
                                            FilterChain filterChain, Authentication authentication) {
        var user = ((User) authentication.getPrincipal());

        var roles = user.getAuthorities()
            .stream()
            .map(GrantedAuthority::getAuthority)
            .collect(Collectors.toList());

        var signingKey = SecurityConstants.JWT_SECRET.getBytes();

        var token = Jwts.builder()
            .signWith(Keys.hmacShaKeyFor(signingKey), SignatureAlgorithm.HS512)
            .setHeaderParam("typ", SecurityConstants.TOKEN_TYPE)
            .setIssuer(SecurityConstants.TOKEN_ISSUER)
            .setAudience(SecurityConstants.TOKEN_AUDIENCE)
            .setSubject(user.getUsername())
            .setExpiration(new Date(System.currentTimeMillis() + 864000000))
            .claim("rol", roles)
            .compact();

        response.addHeader(SecurityConstants.TOKEN_HEADER, SecurityConstants.TOKEN_PREFIX + token);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第二个过滤器处理所有 HTTP 请求，并检查是否有带有正确令牌的授权头。例如，如果令牌未过期或签名密钥正确。

如果令牌有效，那么过滤器会将认证数据添加到 Spring 的安全上下文中。

```
public class JwtAuthorizationFilter extends BasicAuthenticationFilter {

    private static final Logger log = LoggerFactory.getLogger(JwtAuthorizationFilter.class);

    public JwtAuthorizationFilter(AuthenticationManager authenticationManager) {
        super(authenticationManager);
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
                                    FilterChain filterChain) throws IOException, ServletException {
        var authentication = getAuthentication(request);
        if (authentication == null) {
            filterChain.doFilter(request, response);
            return;
        }

        SecurityContextHolder.getContext().setAuthentication(authentication);
        filterChain.doFilter(request, response);
    }

    private UsernamePasswordAuthenticationToken getAuthentication(HttpServletRequest request) {
        var token = request.getHeader(SecurityConstants.TOKEN_HEADER);
        if (StringUtils.isNotEmpty(token) && token.startsWith(SecurityConstants.TOKEN_PREFIX)) {
            try {
                var signingKey = SecurityConstants.JWT_SECRET.getBytes();

                var parsedToken = Jwts.parser()
                    .setSigningKey(signingKey)
                    .parseClaimsJws(token.replace("Bearer ", ""));

                var username = parsedToken
                    .getBody()
                    .getSubject();

                var authorities = ((List<?>) parsedToken.getBody()
                    .get("rol")).stream()
                    .map(authority -> new SimpleGrantedAuthority((String) authority))
                    .collect(Collectors.toList());

                if (StringUtils.isNotEmpty(username)) {
                    return new UsernamePasswordAuthenticationToken(username, null, authorities);
                }
            } catch (ExpiredJwtException exception) {
                log.warn("Request to parse expired JWT : {} failed : {}", token, exception.getMessage());
            } catch (UnsupportedJwtException exception) {
                log.warn("Request to parse unsupported JWT : {} failed : {}", token, exception.getMessage());
            } catch (MalformedJwtException exception) {
                log.warn("Request to parse invalid JWT : {} failed : {}", token, exception.getMessage());
            } catch (SignatureException exception) {
                log.warn("Request to parse JWT with invalid signature : {} failed : {}", token, exception.getMessage());
            } catch (IllegalArgumentException exception) {
                log.warn("Request to parse empty or null JWT : {} failed : {}", token, exception.getMessage());
            }
        }

        return null;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 安全配置

我们需要配置的最后一部分是 Spring Security 本身。配置很简单，我们只需要设置一些细节:

*   密码编码器——在我们的例子中是 bcrypt
*   [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 配置
*   认证管理器——在我们的例子中是简单的内存认证，但在现实生活中，您将需要类似于[userdailsservice](https://www.baeldung.com/spring-security-authentication-with-a-database)的东西
*   设置哪些端点是安全的，哪些是公开可用的
*   将我们的 2 个过滤器添加到安全上下文中
*   禁用会话管理–我们不需要会话，因此这将防止创建会话 cookies

```
@EnableWebSecurity
@EnableGlobalMethodSecurity(securedEnabled = true)
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.cors().and()
            .csrf().disable()
            .authorizeRequests()
            .antMatchers("/api/public").permitAll()
            .anyRequest().authenticated()
            .and()
            .addFilter(new JwtAuthenticationFilter(authenticationManager()))
            .addFilter(new JwtAuthorizationFilter(authenticationManager()))
            .sessionManagement()
            .sessionCreationPolicy(SessionCreationPolicy.STATELESS);
    }

    @Override
    public void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
            .withUser("user")
            .password(passwordEncoder().encode("password"))
            .authorities("ROLE_USER");
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public CorsConfigurationSource corsConfigurationSource() {
        final UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", new CorsConfiguration().applyPermitDefaultValues());

        return source;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 测试

#### 请求公共 API

```
GET http://localhost:8080/api/public 
```

Enter fullscreen mode Exit fullscreen mode

```
HTTP/1.1 200 
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Type: text/plain;charset=UTF-8
Content-Length: 32
Date: Sun, 13 Jan 2019 12:22:14 GMT

Hello from public API controller

Response code: 200; Time: 18ms; Content length: 32 bytes 
```

Enter fullscreen mode Exit fullscreen mode

#### 认证用户

```
POST http://localhost:8080/api/authenticate?username=user&password=password 
```

Enter fullscreen mode Exit fullscreen mode

```
HTTP/1.1 200 
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJpc3MiOiJzZWN1cmUtYXBpIiwiYXVkIjoic2VjdXJlLWFwcCIsInN1YiI6InVzZXIiLCJleHAiOjE1NDgyNDYwNzUsInJvbCI6WyJST0xFX1VTRVIiXX0.yhskhWyi-PgIluYY21rL0saAG92TfTVVVgVT1afWd_NnmOMg__2kK5lcna3lXzYI4-0qi9uGpI6Ul33-b9KTnA
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Length: 0
Date: Sun, 13 Jan 2019 12:21:15 GMT

<Response body is empty>

Response code: 200; Time: 167ms; Content length: 0 bytes 
```

Enter fullscreen mode Exit fullscreen mode

#### 用令牌请求私有 API

```
GET http://localhost:8080/api/private
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJpc3MiOiJzZWN1cmUtYXBpIiwiYXVkIjoic2VjdXJlLWFwcCIsInN1YiI6InVzZXIiLCJleHAiOjE1NDgyNDI1ODksInJvbCI6WyJST0xFX1VTRVIiXX0.GzUPUWStRofrWI9Ctfv2h-XofGZwcOog9swtuqg1vSkA8kDWLcY3InVgmct7rq4ZU3lxI6CGupNgSazypHoFOA 
```

Enter fullscreen mode Exit fullscreen mode

```
HTTP/1.1 200 
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Type: text/plain;charset=UTF-8
Content-Length: 33
Date: Sun, 13 Jan 2019 12:22:48 GMT

Hello from private API controller

Response code: 200; Time: 12ms; Content length: 33 bytes 
```

Enter fullscreen mode Exit fullscreen mode

#### 对私有 API 的请求没有令牌

当您在没有有效 JWT 的情况下调用安全端点时，您将得到 HTTP 403 消息。

```
GET http://localhost:8080/api/private 
```

Enter fullscreen mode Exit fullscreen mode

```
HTTP/1.1 403 
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Type: application/json;charset=UTF-8
Transfer-Encoding: chunked
Date: Sun, 13 Jan 2019 12:27:25 GMT

{
  "timestamp": "2019-01-13T12:27:25.020+0000",
  "status": 403,
  "error": "Forbidden",
  "message": "Access Denied",
  "path": "/api/private"
}

Response code: 403; Time: 28ms; Content length: 125 bytes 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

本文的目标并不是展示如何在 Spring Security 中使用 jwt 的正确方法。这是一个如何在实际应用中做到这一点的例子。此外，我不想太深入这个主题，因为令牌刷新、失效等内容很少。但我可能会在将来讨论这些话题。

**TL；你可以在我的 [GitHub 库](https://github.com/kubadlo/jwt-security)中找到这个示例 API 的完整源代码。**