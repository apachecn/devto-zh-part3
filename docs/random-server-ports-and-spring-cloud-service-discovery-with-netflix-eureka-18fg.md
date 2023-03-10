# 网飞尤里卡的随机服务器端口和 Spring 云服务发现

> 原文：<https://dev.to/jmlw/random-server-ports-and-spring-cloud-service-discovery-with-netflix-eureka-18fg>

[![Random Server Ports and Spring Cloud Service Discovery with Netflix Eureka](img/7987e8727071d72a7a0021019b7c2a4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IamVumeF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1501823129913-a386fe76d87a%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

最近，我遇到了一个问题，我希望能够在本地运行多个 spring boot 服务来进行测试和开发。不幸的是，它们都运行在同一个端口上，所以无法启动！

解决这个问题非常简单，在我们的 Spring Boot 应用程序中，我们只需在我们的`application.yml`中配置一个服务器端口，如下所示:

```
server:
    port: 0 
```

我们可以选择`0`作为启动时随机选择的端口。如果我们想指定另一个端口，我们可以手动将其设置为`8081`或`8082`等...然而，手动指定端口是很痛苦的，尤其是当您有许多想要运行的服务时。

手动指定端口的另一个缺点是部署。当您将应用程序部署到服务器(本地或云中)时，您必须确保应用程序部署到的服务器上没有端口冲突。对于负责部署服务的团队来说，这是一项乏味且耗时的工作，对于记录应用程序所需端口的开发人员来说，这也是一件令人烦恼的事情。更复杂的是，假设您正在 docker 容器中部署，并使用容器编排框架。如果应用程序成功部署，您很可能无法提前知道它将实际部署在哪个节点上。在这种情况下，您的 orchestrator 可能会遇到端口冲突，或者由于缺少容器端口可用的节点而无法供应您的应用程序。

设置随机端口也有不好的一面。很难在本地访问您的服务，因为重启后它总是在不同的端口上。这可以通过使用网关来解决，如 Spring Cloud Gateway，并使用 Spring Cloud Discovery 和网飞尤里卡来配置自动发现，以通过公共网关端口将流量定向到您的应用程序。同样，我们会遇到另一个问题，您的应用程序在初始化期间会将端口`0`(随机)报告给 Eureka 作为它运行的端口，导致无法访问的服务。

这不是我们想要的，所以我们需要一种方法来定制应用程序的初始化，为我们选择一个随机端口，并允许 Eureka / Service Discovery 用这个新的随机端口配置自己。我们可以在 Spring Boot 2 (Spring Framework 5+)中通过如下扩展`WebServerFactoryCustomizer`类来实现这一点:

```
@Configuration
public class WebServerFacotryCustomizerConfiguration implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> {

    @Value("${port.number.min:8080}")
    private Integer minPort;

    @Value("${port.number.max:8090}")
    private Integer maxPort;

    @Override
    public void customize(ConfigurableServletWebServerFactory factory) {
        int port = SocketUtils.findAvailableTcpPort(minPort, maxPort);
        factory.setPort(port);
        System.getProperties().put("server.port", port);
    }
} 
```

这里，我们将使用 Spring 的 SocketUtils 来查找我们范围内可用的端口，在 servlet web 服务器工厂配置中设置该端口，并将其设置为系统属性。这样做将确保我们的应用程序将获得一个不与已在使用的端口冲突的可用端口，并且它还将允许我们的服务发现正确地初始化自己！

另外，我还没有在生产环境中测试过，也没有以任何方式部署过，所以可能会有一些问题。这种解决方案在您的部署过程中可能也很难使用，因为部署过程可能需要提前了解应用程序的端口。然而，如果您的服务都依赖于 Eureka 进行服务发现，Eureka 将报告正确的应用程序端口和 IP 地址，以便您的服务可以到达。只要您的服务不需要额外的端口转发，您的端口就可以在主机外部到达，这应该就可以了。