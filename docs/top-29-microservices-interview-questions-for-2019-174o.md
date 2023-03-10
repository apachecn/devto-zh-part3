# 2020 年 29 大微服务面试问题

> 原文：<https://dev.to/lambdatest/top-29-microservices-interview-questions-for-2019-174o>

自 2011 年问世以来，微服务掀起了巨大的波澜，尤其是在开发前瞻性应用的组织中。Nginx 的一项调查显示，目前有 36%的企业在使用微服务，另有 26%的企业正在研究如何实现微服务。有几个原因会使微服务成为完美的领域，如果你想改变你的技术或试图学习一种新的技术。让我们来讨论在不同的面试中向新手和有经验的开发人员提出的 29 个微服务面试问题。

## 1。什么是春云？

大概，你可能遇到的第一个微服务面试问题。微服务中的 Spring Cloud 是一个提供与外部系统集成的系统。它是一个短命的框架，以快速的方式构建应用程序。与有限的数据处理量相关联，它在微服务架构中起着非常重要的作用。

对于典型的使用案例，Spring cloud 提供了一些开箱即用的体验和一组广泛的功能，如下所述:

*   版本化和分布式配置

*   服务注册的发现

*   服务对服务呼叫

*   按指定路线发送

*   断路器和负载平衡

*   集群国家和领导选举

*   全局锁和分布式消息传递

**注:-** [**十进制转格雷码**](https://www.lambdatest.com/free-online-tools/decimal-to-gray-code?utm_source=devto&utm_medium=organic&utm_campaign=apr14_kj&utm_term=kj&utm_content=free_tools) -免费在线工具，可将任意十进制数转换成格雷码格式。这个工具可以在任意基数和格雷码之间转换。

## 2。什么是 Spring Boot？

弹簧靴是微服务面试问题中的一个主要话题。随着新功能的加入，Spring 变得更加复杂。每当您开始一个新项目时，都必须添加一个新的构建路径或 Maven 依赖项。简而言之，你需要从头开始做所有的事情。Spring Boot 是解决方案，将帮助您避免所有的代码配置。在回答这种常见的微服务面试问题时，通过实时示例进行解释会更好。如果是做菜，食材可以认为是春天。而 Spring Boot 是完全煮熟的食谱。

## 3。如何覆盖 Spring Boot 项目的默认属性？

这可以通过在 application.properties.
中指定属性来实现，例如，在 Spring MVC 应用程序中，您必须指定后缀和前缀。这可以通过在 application.properties 中输入下面提到的属性来完成。

For 后缀— spring.mvc.view.suffix:。JSP
For prefix–spring . MVC . view . prefix:/we b-INF/

## 4。执行机构在 Spring Boot 的作用

这是最重要的特性之一，它帮助您访问在生产环境中运行的应用程序的当前状态。有多种指标可用于检查当前状态。它们还提供了 restful web 服务的端点，可以简单地用于检查不同的指标。

## 5。Spring 安全性是如何在 Spring Boot 应用程序中实现的？

实施只需要最少的配置。您需要做的就是在 pom.xml 文件中添加 Spring-boot-starter-security starter。您还需要创建一个 Spring config 类，它将覆盖所需的方法，同时扩展 WebSecurityConfigurerAdapter 以实现应用程序中的安全性。以下是相同的示例代码:

```
package com.gkatzioura.security.securityendpoints.config;

import org.springframework.context.annotation.Configuration;

import org.springframework.security.config.annotation.web.builders.HttpSecurity;

import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

[@Configuration](http://twitter.com/Configuration)

public class SecurityConfig extends WebSecurityConfigurerAdapter {

    [@Override](http://twitter.com/Override)

    protected void configure(HttpSecurity http) throws Exception {

        http.authorizeRequests()

            .antMatchers("/welcome").permitAll()

            .anyRequest().authenticated()

            .and()

            .formLogin()

            .permitAll()

            .and()

            .logout()

            .permitAll();

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 
  

1.  What embedded containers does Spring Boot support?

无论何时创建 Java 应用程序，都可以通过两种方法进行部署:

*   通过使用外部的应用程序容器

*   您还可以将容器嵌入到 jar 文件中。

Spring Boot 包含 Jetty、Tomcat 和 undertow 服务器，它们都是嵌入式的。

Jetty 用于许多项目，Eclipse Jetty 可以嵌入到框架和应用服务器、工具以及集群中。

Tomcat — Apache Tomcat 是一个开源的 JavaServer pages 实现，可以很好地与嵌入式系统一起工作。

**Undertow** —一个灵活而卓越的 web 服务器，它使用小型的单个处理程序来开发 web 服务器。

**注:-**[**十进制到 BCD**](https://www.lambdatest.com/free-online-tools/decimal-to-bcd?utm_source=devto&utm_medium=organic&utm_campaign=apr14_kj&utm_term=kj&utm_content=free_tools) (二进制编码的十进制)转换器允许用户将十进制整数转换为 BCD。

## 7。微服务端到端测试是什么意思？

[端到端测试](https://www.lambdatest.com/blog/all-you-need-to-know-about-end-to-end-testing/?utm_source=devto&utm_medium=organic&utm_campaign=apr14_kj&utm_term=kj&utm_content=blog)验证工作流中的所有流程，以检查一切是否按预期正常工作。它还确保系统以统一的方式工作，从而满足业务需求。

## 8。什么是语义监控？

它结合了对整个应用程序的监控和自动化测试。语义监控的主要好处是找出对您的业务更有利的因素。
语义监控和服务层监控从业务的角度对微服务进行监控。一旦检测到问题，它们允许更快的隔离和[错误分类](https://www.lambdatest.com/blog/all-about-triaging-bugs/?utm_source=devto&utm_medium=organic&utm_campaign=apr14_kj&utm_term=kj&utm_content=blog)，从而减少修复所需的主要时间。它对服务层和事务进行分类，以找出受可用性或低性能影响的事务。

## 9。如何设置服务发现？

有多种方法可以设置服务发现。我会选择一个我认为最有效的，网飞的尤利卡。这是一个没有麻烦的过程，不会对应用程序造成很大的影响。此外，它支持多种类型的 web 应用程序。

Eureka 配置包括 2 个步骤—客户端配置和服务器配置。使用属性文件可以轻松完成客户端配置。在类路径中，Eureka 搜索 eureka-client.properties。它还在特定于环境的属性文件中搜索由环境引起的覆盖。

对于服务器配置，您必须首先配置客户端。完成后，服务器启动一个客户机，用来查找其他服务器。默认情况下，Eureka 服务器使用客户机配置来查找对等服务器。

## 10。你为什么会选择微服务架构？

一个你应该准备好的非常常见的微服务面试问题！微服务架构有很多优点。以下是其中的几个例子:

*   微服务可以很容易地适应其他框架或技术。

*   单个进程的故障不会影响整个系统。

*   为大企业和小团队提供支持。

*   可以在相对较短的时间内独立部署。

## 11。微服务中为什么需要报告&仪表盘？

报告和仪表盘主要用于监控和维护微服务。有多种工具可以帮助实现这一目的。[报告](https://www.lambdatest.com/blog/advanced-guide-on-writing-a-bug-report/?utm_source=devto&utm_medium=organic&utm_campaign=apr14_kj&utm_term=kj&utm_content=blog)和仪表板可用于:

*   找出哪些微服务暴露了哪些资源。

*   找出每当组件发生变化时受到影响的服务。

*   提供一个方便的点，以便在需要文档时可以访问。

*   部署的组件版本。

*   从组件中获得一种成熟和遵从的感觉。

## 12。为什么人们在使用微服务时会犹豫？

我见过很多人在这个问题上摸不着头脑。毕竟，我们是作为微服务架构师出现在微服务面试问题上的，所以承认缺点总是有点棘手。下面是答案:

*   **需要大量投资** —微服务需要大量协作。因为你的团队是独立工作的，他们应该能够在任何时候都很好地同步。

*   **重度架构搭建** —系统分布式，架构重度参与。

*   **处理运营开销的过度规划** —如果您计划使用微服务架构，您需要为运营开销做好准备。

*   **自主人员选择** —需要能够支持异构分布的微服务的熟练专业人员。

## 13。PACT 是如何工作的？

PACT 是一个开源工具。它有助于测试消费者和服务提供商之间的交互。但是，它不包含在合同中，增加了应用程序的可靠性。消费者服务开发人员从编写一个定义与服务提供者交互模式的测试开始。测试包括提供者的状态、请求体和预期的响应。基于它，PACT 创建一个存根，根据它执行测试。输出存储在一个 JSON 文件中。

## 14。定义领域驱动的设计

主要关注核心域逻辑。基于领域的模型检测复杂的设计。不断与领域专家合作，解决与领域相关的问题，并改进应用程序的模型。在回答这个微服务面试问题的同时，你还需要提到 DDD 的核心基本面。他们是

*   DDD 主要关注领域逻辑和领域本身。

*   复杂的设计完全基于领域的模型。

*   为了改进模型的设计和修复任何新出现的问题，DDD 不断地与领域专家合作。

## 15。什么是耦合和内聚？

耦合可以被认为是组件依赖关系之间强度的度量。一个好的微服务应用程序设计总是由低耦合和高内聚组成。

在前一个问题之后，面试官通常会问凝聚力。它也是另一种测量单位。更像是模块内的元件保持结合在一起的程度。

如果你出现在微服务面试问题中，那么必须记住设计微服务的一个重要关键是低耦合和高内聚的组合。当松散耦合时，一个服务对其他服务知之甚少。这保持了服务的完整性。在高内聚中，将所有相关的逻辑保存在一个服务中成为可能。否则，服务将试图相互通信，从而影响整体性能。

## 16。OAuth 是什么？

开放授权协议，也称为 OAuth，有助于通过 HTTP 使用第三方协议(如脸书、GitHub 等)访问客户端应用程序。您还可以在不同站点之间共享资源，而不需要凭据。

OAuth 允许像脸书这样的第三方使用最终用户的帐户信息，同时保持其安全性(不使用或暴露用户的密码)。它更像是代表用户的中介，同时向服务器提供令牌来访问所需的信息。

## 17。为什么微服务需要容器？

要管理基于微服务的应用程序，容器是最简单的选择。它帮助用户单独部署和开发。还可以使用 Docker 将微服务封装在容器的映像中。无需任何额外的依赖或努力，微服务就可以使用这些元素。

## 18。访问 RESTful 微服务的途径有哪些？

这是另一个常见的微服务面试问答是如何访问 RESTful 微服务？你可以通过两种方法做到这一点

*   使用负载平衡的 rest 模板。

*   使用多个微服务。

## 19。微服务测试的主要障碍是什么？

谈到缺点，这是你可能准备好的另一个微服务面试问题，将围绕测试微服务时面临的[挑战。](https://www.lambdatest.com/blog/testing-challenges-related-to-microservice-architecture/?utm_source=devto&utm_medium=organic&utm_campaign=apr14_kj&utm_term=kj&utm_content=blog)

*   在开始编写集成测试的测试用例之前，测试人员应该对所有的入站和出站流程有一个透彻的理解。

*   当独立的团队在不同的功能上工作时，协作可能会被证明是一项非常艰难的任务。很难找到一个空闲的时间窗口来执行一轮完整的[回归测试](https://www.lambdatest.com/blog/regression-testing-strategies-of-mobile-web-pages/?utm_source=devto&utm_medium=organic&utm_campaign=apr14_kj&utm_term=kj&utm_content=blog)。

*   随着微服务数量的增加，系统的复杂度也随之增加。

*   在从单一架构转换的过程中，测试人员必须确保组件之间的内部通信没有中断。

## 20。过渡到微服务时的常见错误

不仅仅是在开发上，错误往往也存在于过程中。任何有经验的面试官都会在微服务面试问题的队列下有这个。一些常见的错误是

*   开发人员通常无法概括当前的挑战。

*   重写已经存在的程序。

*   职责、时间表和界限没有明确定义。

*   未能从一开始就实现和确定自动化的范围。

**注:-** [**格雷码转换成十进制**](https://www.lambdatest.com/free-online-tools/gray-to-decimal?utm_source=devto&utm_medium=organic&utm_campaign=apr14_kj&utm_term=kj&utm_content=free_tools) -免费在线工具，可将任意格雷码格式转换成十进制数。这个工具可以在任何格雷码和基数之间转换。

## 21。微服务设计的根本是什么？

这大概是最常被问到的微服务面试问题之一。回答这个问题时，你需要记住以下几点:

*   定义范围

*   将松散耦合与高内聚结合起来

*   创建一个唯一的服务，它将作为一个标识源，就像数据库表中的唯一键一样

*   创建正确的 API，并在集成过程中特别小心。

*   限制对数据的访问，并将其限制在所需的级别

*   保持请求和响应之间的顺畅流动

*   自动化大多数流程以降低时间复杂性

*   将表的数量保持在最低水平，以降低空间复杂性

*   持续监控架构，并在发现任何缺陷时进行修复。

*   应为每个微服务分离数据存储。

*   对于每个微服务，应该有一个独立的构建。

*   将微服务部署到容器中。

*   服务器应该被视为无状态的。

您也可以阅读这篇文章，了解成功微服务设计的 9 个基本要素。

## 22。我们在哪里使用 WebMVC 测试注释？

WebMvcTest 用于对 Spring MVC 应用程序进行单元测试。顾名思义，它完全专注于 Spring MVC 组件。例如，
@ WebMvcTest(value = ToTestController . class，secure = false):
这里的目标是只启动 totest controller。在执行单元测试之前，不会启动其他映射和控制器。

## 23。你说的有界语境是什么意思？

作为领域驱动设计中常见的中心模式，有界上下文是 DDD 的战略设计部分的主要焦点。这完全是关于与大型团队和模型的交易..DDD 通过将大型模型分解成多个有界的上下文来处理它们。在这样做的同时，它也明确地解释了它们之间的关系。

## 24。双因素身份认证有哪些不同类型？

执行双因素身份认证需要 3 种类型的凭据。

1.  一个你知道的东西——比如密码、pin 码或屏幕锁定模式。

2.  您拥有的物理凭证，如 OTP 或电话或 ATM 卡，换句话说，您在外部或第三方设备中拥有的任何种类的凭证。

3.  您的物理身份，如语音认证或生物安全，如指纹或眼睛扫描仪。

## 25。什么是客户端证书？

这是一种通常由客户端系统使用的数字证书，用于发出由远程服务器验证的请求。它在相互的身份验证设计中起着重要的作用，并为请求者的身份提供了强有力的保证。但是，您应该有一个完整配置的后端服务来验证您的客户端证书。

## 26。梅尔文·康威提出的定律意味着什么？

你可能在面试官微服务面试问题清单下面有这个。康威定律陈述“设计系统的组织…被限制生产这些组织的通信结构的复制品的设计。”

面试官可能会问一个反微服务面试问题，如梅尔文·康威定律与微服务有什么关系。嗯，一些松散耦合的 API 形成了微服务的架构。这种结构非常适合小型团队实现自治组件的方式。这种架构使组织在重组工作流程时更加灵活。

## 27。如何配置 Spring Boot 应用程序日志？

Spring Boot 增加了对 Log4J2、Java Util 日志和 Logback 的支持。它通常被预先配置为控制台输出。可以通过在 application.properties 文件中指定 logging.level 来配置它们。

logging . level . spring . framework = Debug

## 28。你将如何执行微服务的安全测试？

在回答这个微服务面试问题之前，向面试官说明微服务是不能整体测试的。你需要独立地测试这些部分。有 3 种常见的程序

**代码扫描** —确保任何一行代码都没有错误，并且可以被复制。

**灵活性** —安全解决方案应该是灵活的，可以根据系统的需求进行调整。

**适应性** —安全协议应该灵活且不断更新，以应对黑客或安全漏洞带来的新威胁。

你也可以关注这篇解释微服务架构对安全性的[影响的博客。](https://www.lambdatest.com/blog/does-microservices-architecture-influence-security-testing/?utm_source=devto&utm_medium=organic&utm_campaign=apr14_kj&utm_term=kj&utm_content=blog)

## 29。什么是幂等性，如何使用？

幂等指的是重复执行一项任务，但最终结果保持不变或相似的场景。

幂等性主要用作数据源或远程服务，当它接收到多个指令时，它只处理一个指令。

那都是我们这边的。但是，如果你在认真思考一个技术切换，不要光靠这些微服务面试问题。因为有经验的面试官也会问交叉问题来检验你有多少深入的知识。参加课程或在线教程，详细研究微服务，这样您就可以回答上述问题以及相关问题。