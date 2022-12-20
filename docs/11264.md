# 借助 HTTPS 和 OAuth 2.0 实现安全的服务对服务 Spring 微服务

> 原文：<https://dev.to/oktadev/secure-service-to-service-spring-microservices-with-https-and-oauth-2-0-2889>

如果使用 Spring Boot、Spring Cloud 和 Spring Cloud Config，用最少的代码构建微服务架构是可能的。将所有东西打包到 Docker 容器中，你可以使用 Docker Compose 运行所有东西。如果您在服务之间通信，您可以通过不在您的`docker-compose.yml`文件中暴露它们的端口来确保您的服务是安全的。

但是如果有人不小心暴露了你的微服务 app 的端口会怎么样呢？他们仍然安全吗？或者任何人都可以访问他们的数据吗？

在这篇文章中，我将向您展示如何使用 HTTPS 和 OAuth 2.0 来保护服务到服务的通信。

## 利用 Spring Boot、Spring Cloud 和 Spring Cloud Config 开发微服务堆栈

我将通过 Spring Boot、Spring Cloud 和 Spring Cloud Config 简化构建完整微服务堆栈的过程。我的好友 Raphael 写了一篇关于如何[构建 Spring 微服务并将其 Dockerize 用于生产](/blog/2019/02/28/spring-microservices-docker)的帖子。您可以使用他的示例应用程序作为起点。克隆[okta-spring-micro services-docker-example](https://github.com/oktadeveloper/okta-spring-microservices-docker-example)项目:

```
git clone https://github.com/oktadeveloper/okta-spring-microservices-docker-example.git spring-microservices-security
cd spring-microservices-security 
```

这个项目需要 Okta 上的两个 OpenID Connect 应用程序，一个用于开发，一个用于生产。如果你没有完成前面提到的教程，你需要在 Okta 上创建每个应用程序。

### 在 Okta 上创建 OpenID Connect 应用

你可以注册一个[免费开发者账户](https://developer.okta.com/signup/)，这将允许你每月 0 美元拥有多达 1000 名活跃用户。对于这个例子来说，这应该足够了。

为什么是 Okta？因为写认证一点都不好玩。Okta 有认证和用户管理 API，可以让你更快地开发你的应用。[我们的 API 和 SDK](https://developer.okta.com/docs/)让您在几分钟内轻松认证、管理和保护您的用户。

创建您的帐户后，在 Okta 的仪表板中创建一个新的 Web 应用程序(**应用程序** > **添加应用程序**)。给应用程序一个你会记得的名字，复制现有的登录重定向 URI，使其使用 HTTPS。点击**完成**。

结果应该类似于下面的截图。

[![OIDC App Settings](img/ea7cbd24755608f6ff946acef9bdb168.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o7xWZNqj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/microservices-service-to-service-security/oidc-web-app-6013d81f99890ddd5f9774a112605db4a6143c1ad6354bc74aed2d84a220fc01.png)

为生产创建另一个应用程序。我把我的叫做`Prod Microservices`。

在您克隆的项目中，修改`config/school-ui.properties`以具有来自您的开发应用程序的设置。

```
okta.oauth2.issuer=https://{yourOktaDomain}/oauth2/default
okta.oauth2.clientId={devClientId}
okta.oauth2.clientSecret={devClientId} 
```

当使用 Maven 单独运行您的应用程序时，将使用这些设置。使用 Docker Compose 运行时会使用生产设置。修改`config-data/school-ui-production.properties`以获得生产应用程序中的设置。

```
okta.oauth2.clientId={prodClientId}
okta.oauth2.clientSecret={prodClientId} 
```

您可以看到`spring.profiles.active`打开了`docker-compose.yml`中的生产配置文件:

```
school-ui:
  image: developer.okta.com/microservice-docker-school-ui:0.0.1-SNAPSHOT
  environment:
    - JAVA_OPTS=
      -DEUREKA_SERVER=http://discovery:8761/eureka
      -Dspring.profiles.active=production
  restart: on-failure
  depends_on:
    - discovery
    - config
  ports:
    - 8080:8080 
```

Docker Compose 从应用程序上面的一个目录中运行，它从一个`config-data`目录中读取数据。因此，您需要将这些属性文件复制到这个目录中。从该项目的根目录运行以下命令。

```
cp config/*.properties config-data/. 
```

## 使用 Docker Compose 启动您的 Spring 微服务堆栈

这个项目在其根目录中有一个聚合器`pom.xml`,它将允许您用一个命令构建所有的项目。运行以下 Maven 命令，为每个项目构建、测试和构建 Docker 映像。

```
mvn clean install 
```

**提示**:如果没有安装 Maven，可以用 [SDKMAN 安装！](https://sdkman.io/) `sdk install maven`

该过程完成后，使用 Docker Compose 启动所有应用程序{配置、发现、学校服务和学校用户界面}。如果没有安装 Docker Compose ，请参见[安装 Docker Compose](https://docs.docker.com/compose/install/)。

```
docker-compose up -d 
```

**提示**:你可以使用 [Kitematic](https://kitematic.com/) 来查看每个应用程序启动时的日志。

在您喜爱的浏览器中导航至`http://localhost:8080`。这样做之后，您应该能够登录并看到学校课程列表。

[![School UI](img/b0e29c31a2a70d79fac563bdc3e1cf77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JS8Ry5bG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/microservices-service-to-service-security/school-ui-f529fbf3e37604e60c44bb7f762f645fe52cc22786a3b6a2b836992c9ba42d5d.png)

### Spring Security 和 OAuth 2.0

这个例子使用了 [Okta 的 Spring Boot 启动器](https://github.com/okta/okta-spring-boot)，它是 Spring Security 上的一个薄层。Okta starter 简化了配置，并在访问令牌中进行受众验证。它还允许您指定将用于创建 Spring 安全授权的声明。

`docker-compose.yml`文件不会将`school-service`暴露给外界。它通过不指定`ports`来做到这一点。

`school-ui`项目有一个`SchoolController`类，它使用 Spring 的`RestTemplate`与`school-service`对话。

```
@GetMapping("/classes")
@PreAuthorize("hasAuthority('SCOPE_profile')")
public ResponseEntity<List<TeachingClassDto>> listClasses() {

    return restTemplate
            .exchange("http://school-service/class", HttpMethod.GET, null,
                    new ParameterizedTypeReference<List<TeachingClassDto>>() {});
} 
```

您会注意到在这个类的端点上有安全性，但是在服务之间不存在安全性。我将在下面的步骤中向您展示如何解决这个问题。

首先，暴露`school-service`的端口来模拟有人篡改配置。更改`docker-compose.yml`中的`school-service`配置以暴露其端口。

```
school-service:
  image: developer.okta.com/microservice-docker-school-service:0.0.1-SNAPSHOT
  environment:
    - JAVA_OPTS=
      -DEUREKA_SERVER=http://discovery:8761/eureka
  depends_on:
    - discovery
    - config
  ports:
    - 8081:8081 
```

用 Docker Compose 重启一切:

```
docker-compose down
docker-compose up -d 
```

您将看到，在`http://localhost:8081`查看数据不需要验证。呀！😱

**在进行下一部分之前，确保**关闭所有 Docker 容器。

```
docker-compose down 
```

## 到处都是 HTTPS！

HTTPS 代表“安全的”HTTP。HTTPS 连接是加密的，其内容比 HTTP 连接更难读取。最近几年，到处都在使用 HTTPS，甚至在发展中国家也是如此。和 HTTPS 一起跑步时，你可能会遇到一些问题，尽早发现这些问题是有好处的。

[让我们加密](https://letsencrypt.org/)是一个提供免费 HTTPS 证书的认证机构。它也有自动更新的 API。简而言之，它使 HTTPS 如此简单，没有理由不使用它！参见[将社交登录添加到您的 JHipster 应用](https://developer.okta.com/blog/2019/02/19/add-social-login-to-spring-boot#configure-the-custom-domain-name-for-your-spring-boot-app)，了解如何使用`certbot`和 Let's Encrypt 生成证书。

我也鼓励你去看看 Spring Boot Starter ACME。这是一个 Spring Boot 模块，它使用加密和自动证书管理环境(ACME)协议简化了证书的生成。

### 使用 mkcert 简化本地 TLS

我最近发现了一个叫做 [mkcert](https://github.com/FiloSottile/mkcert) 的工具，它允许创建`localhost`证书。您可以在 macOS 上使用 Homebrew 安装它:

```
brew install mkcert
brew install nss # Needed for Firefox 
```

如果你在 Linux 上，你需要先安装`certutil`:

```
sudo apt install libnss3-tools 
```

然后使用 [Linuxbrew](http://linuxbrew.sh/) 运行`brew install mkcert`命令。Windows 用户可以用巧克力或勺子使用 T4。

执行下面的`mkcert`命令，为`localhost`、`127.0.0.1`、你的机器名和`discovery`主机(在`docker-compose.yml`中引用)生成一个证书。

```
mkcert -install
mkcert localhost 127.0.0.1 ::1 `hostname` discovery 
```

如果这会生成带有数字的文件，请重命名这些文件，使它们没有数字。

```
mv localhost+2.pem localhost.pem
mv localhost+2-key.pem localhost-key.pem 
```

### HTTPS 与 Spring Boot

Spring Boot 不支持扩展名为 [PEM](https://tools.ietf.org/html/rfc1421) 的证书，但是你可以把它转换成扩展名为`PKCS12`的，Spring Boot 支持。您可以使用 OpenSSL 将证书和私钥转换为 PKCS12。这对于加密生成的证书也是必要的。

运行`openssl`转换证书:

```
openssl pkcs12 -export -in localhost.pem -inkey \
localhost-key.pem -out keystore.p12 -name bootifulsecurity 
```

出现提示时指定密码。

在项目的根目录下创建一个`https.env`文件，并指定以下属性来启用 HTTPS。

```
export SERVER_SSL_ENABLED=true
export SERVER_SSL_KEY_STORE=../keystore.p12
export SERVER_SSL_KEY_STORE_PASSWORD={yourPassword}
export SERVER_SSL_KEY_ALIAS=bootifulsecurity
export SERVER_SSL_KEY_STORE_TYPE=PKCS12 
```

更新`.gitignore`文件以排除`.env`文件，这样密钥库密码就不会出现在源代码控制中。

```
*.env 
```

运行`source https.env`来设置这些环境变量。或者，更好的方法是，将这样的内容添加到您的`.bashrc`或`.zshrc`文件中，以便为每个新 shell 设置这些变量。是的，你也可以把它们包含在每个应用的`application.properties`中，但是这样你就把秘密存储在源代码控制中了。如果您没有将此示例签入源代码管理，这里是您可以复制/粘贴的设置。

```
server.ssl.enabled=true
server.ssl.key-store=../keystore.p12
server.ssl.key-store-password: {yourPassword}
server.ssl.key-store-type: PKCS12
server.ssl.key-alias: bootifulsecurity 
```

启动`discovery`应用程序:

```
cd discovery
source ../https.env
mvn spring-boot:run 
```

然后确认您可以在`https://localhost:8761`访问它。

[![Secure Eureka Server](img/45576e86d75b61324213a6231efccfcd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kfXO7-ea--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/microservices-service-to-service-security/secure-discovery-0b76dd492e27116f77fa34e0771d21f9aa9ef144988b4d093c88845b0bf7a52d.png)

打开`docker-compose.yml`，将`http`的所有实例更改为`https`。编辑`school-ui/src/main/java/…​/ui/controller/SchoolController.java`将呼叫更改为`school-service`以使用 HTTPS。

```
return restTemplate
        .exchange("https://school-service/class", HttpMethod.GET, null,
                new ParameterizedTypeReference<List<TeachingClassDto>>() {}); 
```

更新`{config,school-service,school-ui}/src/main/resources/application.properties`以添加属性，使每个实例[注册为安全应用程序](http://cloud.spring.io/spring-cloud-static/spring-cloud.html#_registering_a_secure_application)。

```
eureka.instance.secure-port-enabled=true
eureka.instance.secure-port=${server.port}
eureka.instance.status-page-url=https://${eureka.hostname}:${server.port}/actuator/info
eureka.instance.health-check-url=https://${eureka.hostname}:${server.port}/actuator/health
eureka.instance.home-page-url=https://${eureka.hostname}${server.port}/ 
```

另外，将每个`application.properties`(和`bootstrap.yml`)中的尤里卡地址改为`https://localhost:8761/eureka`。

**注意**:在`school-ui`项目中的`application.properties`没有指定端口。你需要加上`server.port=8080`。

此时，您应该能够通过在每个项目中运行以下程序来启动所有应用程序(在单独的终端窗口中)。

```
source ../https.env
./mvnw spring-boot:start 
```

在`https://localhost:8080`确认一切正常。然后用`killall java`干掉一切。

## 用 HTTPS 用 Docker 作曲

Docker 不读取环境变量，它不知道您的本地 CA(证书颁发机构)，并且您不能将文件从父目录添加到映像。

要解决这个问题，您需要将`keystore.p12`和`localhost.pem`复制到每个项目的目录中。第一个将用于 Spring Boot，第二个将被添加到每个映像上的 Java 密钥库中。

```
cp localhost.pem keystore.p12 config/.
cp localhost.pem keystore.p12 discovery/.
cp localhost.pem keystore.p12 school-service/.
cp localhost.pem keystore.p12 school-ui/. 
```

然后修改每个项目的`Dockerfile`来复制证书并将其添加到信任存储中。

```
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD target/*.jar app.jar
ADD keystore.p12 keystore.p12
USER root
COPY localhost.pem $JAVA_HOME/jre/lib/security
RUN \
    cd $JAVA_HOME/jre/lib/security \
    && keytool -keystore cacerts -storepass changeit -noprompt \
    -trustcacerts -importcert -alias bootifulsecurity -file localhost.pem
ENV JAVA_OPTS=""
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar"] 
```

然后用 Spring Boot 和 HTTPS 的环境变量创建一个`.env`文件。

```
SERVER_SSL_ENABLED=true SERVER_SSL_KEY_STORE=keystore.p12
SERVER_SSL_KEY_STORE_PASSWORD={yourPassword}
SERVER_SSL_KEY_ALIAS=bootifulsecurity
SERVER_SSL_KEY_STORE_TYPE=PKCS12
EUREKA_INSTANCE_HOSTNAME={yourHostname} 
```

您可以通过运行`hostname`来获得`{yourHostname}`的值。

Docker Compose 有一个“env_file”配置选项，允许您为环境变量读取该文件。更新`docker-compose.yml`为每个应用指定一个`env_file`。

```
version: '3'
services:
  discovery:
    env_file:
      - .env
    ...
  config:
    env_file:
      - .env
    ...
  school-service:
    env_file:
      - .env
    ...
  school-ui:
    env_file:
      - .env
    ... 
```

您可以通过从根目录运行`docker-compose config`来确保它正在工作。

运行`mvn clean install`重建您所有的 Docker 图像，启用 HTTPS 进行 Eureka 注册。然后开始所有的一切。

```
docker-compose up -d 
```

现在你所有的应用程序都在 HTTPS 的 Docker 上运行了！在`https://localhost:8080`证明。

**注意**:如果你的应用程序不能启动或者不能相互通信，确保你的主机名与你在`.env`中的相匹配。

您还可以进行一项安全性改进:使用 OAuth 2.0 来保护您的学校服务 API。

## OAuth 2.0 的 API 安全性

将 Okta Spring Boot 启动器和 Spring Cloud 配置添加到`school-service/pom.xml`:

```
<dependency>
    <groupId>com.okta.spring</groupId>
    <artifactId>okta-spring-boot-starter</artifactId>
    <version>1.1.0</version>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency> 
```

然后在`school-service/src/main/java/…​/service/configuration`中创建一个`SecurityConfiguration.java`类:

```
package com.okta.developer.docker_microservices.service.configuration;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests().anyRequest().authenticated()
            .and()
            .oauth2ResourceServer().jwt();
    }
} 
```

创建一个`school-service/src/test/resources/test.properties`文件并添加属性，这样 Okta 的配置就通过了，并且它在测试时不使用发现或配置服务器。

```
okta.oauth2.issuer=https://{yourOktaDomain}/oauth2/default
okta.oauth2.clientId=TEST
spring.cloud.discovery.enabled=false
spring.cloud.config.discovery.enabled=false
spring.cloud.config.enabled=false 
```

然后修改`ServiceApplicationTests.java`来加载测试属性的文件:

```
import org.springframework.test.context.TestPropertySource;

...
@TestPropertySource(locations="classpath:test.properties")
public class ServiceApplicationTests {
    ...
} 
```

添加一个`school-service/src/main/resources/bootstrap.yml`文件，允许这个实例从 Spring Cloud Config 中读取它的配置。

```
eureka:
  client:
    serviceUrl:
      defaultZone: ${EUREKA_SERVER:https://localhost:8761/eureka}
spring:
  application:
    name: school-service
  cloud:
    config:
      discovery:
        enabled: true
        serviceId: CONFIGSERVER
      failFast: true 
```

然后复制`config/school-ui.properties`得到一个等价的`school-service`。

```
cp config/school-ui.properties config/school-service.properties 
```

对于 Docker Compose，您还需要使用以下设置创建一个`config-data/school-service.properties`:

```
okta.oauth2.issuer=https://{yourOktaDomain}/oauth2/default
okta.oauth2.clientId={prodClientId}
okta.oauth2.clientSecret={prodClientId} 
```

您还需要修改`docker-compose.yml`以便`school-service`在失败时重启。

```
school-service:
  ...
  restart: on-failure 
```

提示:你可以在 Okta 上创建一个使用客户端凭证的服务应用，但是这篇文章已经够复杂了。有关该方法的更多信息，请参见[使用 Spring Boot 和 OAuth 2.0 保护服务器到服务器的通信](/blog/2018/04/02/client-creds-with-spring-boot)。

您需要做的最后一步是修改`SchoolController`(在`school-ui`项目中)来添加一个 OAuth 2.0 访问令牌到它对`school-server`的请求中。

向 RestTemplate 添加 AccessToken

```
package com.okta.developer.docker_microservices.ui.controller;

import com.okta.developer.docker_microservices.ui.dto.TeachingClassDto;
import org.springframework.core.ParameterizedTypeReference;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpRequest;
import org.springframework.http.ResponseEntity;
import org.springframework.http.client.ClientHttpRequestExecution;
import org.springframework.http.client.ClientHttpRequestInterceptor;
import org.springframework.http.client.ClientHttpResponse;
import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.security.core.annotation.AuthenticationPrincipal;
import org.springframework.security.oauth2.client.OAuth2AuthorizedClient;
import org.springframework.security.oauth2.client.OAuth2AuthorizedClientService;
import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
import org.springframework.security.oauth2.core.OAuth2AccessToken;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.servlet.ModelAndView;

import java.io.IOException;
import java.util.List;

@Controller
@RequestMapping("/")
public class SchoolController {

    private final OAuth2AuthorizedClientService authorizedClientService;
    private final RestTemplate restTemplate;

    public SchoolController(OAuth2AuthorizedClientService clientService,
                            RestTemplate restTemplate) { (1)
        this.authorizedClientService = clientService;
        this.restTemplate = restTemplate;
    }

    @RequestMapping("")
    public ModelAndView index() {
        return new ModelAndView("index");
    }

    @GetMapping("/classes")
    @PreAuthorize("hasAuthority('SCOPE_profile')")
    public ResponseEntity<List<TeachingClassDto>> listClasses(
            @AuthenticationPrincipal OAuth2AuthenticationToken authentication) { (2)

        OAuth2AuthorizedClient authorizedClient =
                this.authorizedClientService.loadAuthorizedClient(
                        authentication.getAuthorizedClientRegistrationId(),
                        authentication.getName()); (3)

        OAuth2AccessToken accessToken = authorizedClient.getAccessToken(); (4)
        restTemplate.getInterceptors().add(getBearerTokenInterceptor(accessToken.getTokenValue())); (5)

        return restTemplate
                .exchange("https://school-service/class", HttpMethod.GET, null,
                        new ParameterizedTypeReference<List<TeachingClassDto>>() {});
    }

    private ClientHttpRequestInterceptor getBearerTokenInterceptor(String accessToken) {
        return (request, bytes, execution) -> {
            request.getHeaders().add("Authorization", "Bearer " + accessToken);
            return execution.execute(request, bytes);
        };
    }
} 
```

**1** 向构造函数
**2** 添加一个`OAuth2AuthenticationToken`到`listClasses()`方法
**3** 从`authentication`
**创建一个`OAuth2AuthorizedClient`4**从授权客户端获取访问令牌
**5** 将访问令牌添加到`Authorization`头

就是这样！由于`school-ui`和`school-service`使用相同的 OIDC 应用程序设置，服务器将识别并验证访问令牌(也是 JWT)，并允许访问。

此时，您可以选择使用`./mvnw spring-boot:run`或 Docker Compose 单独运行所有应用程序。后一种方法只需要几个命令。

```
mvn clean install docker-compose down
docker-compose up -d 
```

## 使用 HTTP Basic Auth 与 Eureka 和 Spring Cloud Config 进行安全微服务通信

为了提高微服务、Eureka 服务器和 Spring Cloud Config 之间的安全性，甚至可以添加 HTTP 基本认证。为此，您需要在`config`和`discovery`项目中添加`spring-boot-starter-security`作为依赖项。然后您需要为每个文件指定一个`spring.security.user.password`并加密它。你可以在 [Spring Cloud Config 的安全文档](https://cloud.spring.io/spring-cloud-config/single/spring-cloud-config.html#_security)中了解更多关于如何做到这一点的信息。

一旦在两个项目中都配置了 Spring Security，就可以调整 URL，在其中包含用户名和密码。例如，下面是`school-ui`项目的`bootstrap.yml`中的设置:

```
eureka:
  client:
    serviceUrl:
      defaultZone: ${EUREKA_SERVER:https://username:password@localhost:8761/eureka} 
```

您需要对`docker-compose.yml`中的 URL 进行类似的调整。

## 增强您对 Spring 微服务、Docker 和 OAuth 2.0 的了解

本教程向您展示了如何确保您的服务到服务通信在微服务架构中是安全的。您了解了如何在任何地方使用 HTTPS，以及如何用 OAuth 2.0 和 JWTs 锁定您的 API。

你可以在 GitHub 的[okta developer/okta-spring-micro services-https-example](https://github.com/oktadeveloper/okta-spring-microservices-https-example)找到这个例子的源代码。

如果你想更深入地探讨这些话题，我想你会喜欢下面的博文:

*   [构建 Spring 微服务并将其用于生产](/blog/2019/02/28/spring-microservices-docker)

*   [用 Spring Boot 为微啤酒构建微服务架构](/blog/2017/06/15/build-microservices-architecture-spring-boot)

*   [使用 Spring Boot 2.0 和 OAuth 2.0 构建和保护微服务](/blog/2018/05/17/microservices-spring-boot-2-oauth)

*   [使用 OAuth 2.0 和 JHipster 开发微服务架构](/blog/2018/03/01/develop-microservices-jhipster-oauth)

*   [通过 Spring Boot 和 OAuth 2.0 实现安全的服务器到服务器通信](/blog/2018/04/02/client-creds-with-spring-boot)

这些博客帖子有助于让本文中的所有内容正常工作:

*   [使用 Spring Cloud 安全发现网飞尤里卡](https://piotrminkowski.wordpress.com/2018/05/21/secure-discovery-with-spring-cloud-netflix-eureka/)

*   [让我们加密保护 Spring Boot](https://dzone.com/articles/spring-boot-secured-by-lets-encrypt)

有问题吗？在下面的评论里问他们吧！如果你的问题与本文无关，请将它们发布到我们的[开发者论坛](https://devforum.okta.com/)。

要获得更多科技博客文章的通知，请在 Twitter 上关注我们 [@oktadev](https://twitter.com/oktadev) ，或者订阅我们的 [YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。