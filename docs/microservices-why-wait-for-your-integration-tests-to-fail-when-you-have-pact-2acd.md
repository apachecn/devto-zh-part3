# 微服务——有了 Pact，为什么还要等待集成测试失败呢？

> 原文：<https://dev.to/shyamala_u/microservices-why-wait-for-your-integration-tests-to-fail-when-you-have-pact-2acd>

随着越来越多的软件从整体迁移到微服务，我们淹没在比以往更多的 API 的世界里。如何才能写出有意义、精确、可测试、可维护的 API？一旦一个 API 发布了，我们就有义务不破坏它们，或者我们必须事先知道我们有突破性的改变。

在这篇文章中，我试图将我的经验整合起来，讲述 Pact([https://docs . Pact . io](https://docs.pact.io))如何帮助我们在微服务架构中编写更好的 API。

## 什么是契约？

> 个人或政党之间的正式协议。

按 [`Pact Foundation`](https://pact.io)

> 约定是一个`contract testing tool`。契约测试是一种确保服务(比如 API 提供者和消费者)可以相互通信的方法。如果没有契约测试，知道服务可以通信的唯一方法就是使用昂贵而脆弱的集成测试。

## 当事人

任何 API(应用程序编程接口)都由两个重要部分组成:

**提供者:**公开一个或多个 API 的人

**消费者**:使用由提供者公开的 API 的一个或多个客户端

## 契约是如何运作的？

`Consumer captures an expectation`作为单独的契约，以及`provider agrees to it`。

## 使用契约的优势

### 隔离:

独立测试提供者和消费者**但同时测试它们**的能力，即契约，为我们提供了

*   在开发人员的机器上使用提供商的 API 测试消费者，而无需进行实际调用。
*   针对一个或多个使用者测试提供者的 API 更改，以确保这些更改不会意外破坏现有的使用者

### 快速反馈

我们不必等待我们的端到端测试失败，同样的反馈可以被单元测试。

### 以用户为中心的 API 设计

契约本质上是`Consumer Driven Contracts`的，所以消费者列出了期望，这导致了更好的可用 API

### 现有 API 概述

*   pacts 提供`Network Graph`依赖服务
*   契约有助于我们理解，如果`one or more APIs are similar`
*   当消费者不再使用契约时，契约也可以显示`unused APIs`。

### 语言独立

Pact 是一个[规范](https://github.com/pact-foundation/pact-specification#index)，这使得它非常适合微服务测试。你可以在这里找到用许多编程语言实现的 Pact 消费者和提供者库

## 约定在行动:

### 一个约定的例子

让我们以认证即服务这一最简单的用例为例。

消费者:我需要让用户登录，我有用户凭证
提供者:我可以验证给定凭证的用户

为了演示，我们使用如上所述的相同约定。完整的实现可以在[这里](https://github.com/shyamz-22/Pactit)找到

### 消费者约定在 Go:

```
 import (
        "fmt"
        "github.com/pact-foundation/pact-go/dsl"
        "net/http"
        "testing"
    )

    func TestClient_AuthenticateUser(t *testing.T) {

        var username = "alice"
        var password = "s3cr3t"

        t.Run("user exists", func(t *testing.T) {
            pact := &dsl.Pact{
            Consumer: "Quoki",
            Provider: "UserManager",
          PactDir:  "../pacts",
          LogDir:   "../pacts/logs",
        }

      defer pact.Teardown()

            pact.
                AddInteraction().
                Given("user exists").
                UponReceiving("a request to authenticate").
                WithRequest(dsl.Request{
                    Method:  "POST",
                    Path:    dsl.String(fmt.Sprintf("/users/%s/authentication", username)),
                    Headers: dsl.MapMatcher{"Content-Type": dsl.Like("application/x-www-form-urlencoded")},
                    Body: dsl.MapMatcher{
                    "password": dsl.Like(password),
                    },
                }).
                WillRespondWith(dsl.Response{
                    Status: http.StatusNoContent,
                })

        pact.Verify(func() error {
                subject := New(fmt.Sprintf("http://localhost:%d", pact.Server.Port))
                ok := subject.AuthenticateUser(username, password)

                if !ok {
                    t.Fail()
                }

                return nil
            })

        })
    } 
```

成功运行将生成，

```
 {  "consumer":  {  "name":  "Quoki"  },  "provider":  {  "name":  "UserManager"  },  "interactions":  [  {  "description":  "a request to authenticate",  "providerState":  "user exists",  "request":  {  "method":  "POST",  "path":  "/users/alice/authentication",  "headers":  {  "Content-Type":  "application/x-www-form-urlencoded"  },  "body":  "password=s3cr3t",  "matchingRules":  {  "$.headers.Content-Type":  {  "match":  "type"  },  "$.body.password":  {  "match":  "type"  }  }  },  "response":  {  "status":  204,  "headers":  {  }  }  }  ],  "metadata":  {  "pactSpecification":  {  "version":  "2.0.0"  }  }  } 
```

### 科特林中契约的提供者验证

然后，提供者将此作为需求，然后通过运行此测试
进行验证

```
 package com.shyamz.provider.authenticate

    import au.com.dius.pact.provider.junit.Consumer
    import au.com.dius.pact.provider.junit.Provider
    import au.com.dius.pact.provider.junit.State
    import au.com.dius.pact.provider.junit.loader.PactFolder
    import au.com.dius.pact.provider.junit.target.HttpTarget
    import au.com.dius.pact.provider.junit.target.Target
    import au.com.dius.pact.provider.junit.target.TestTarget
    import au.com.dius.pact.provider.spring.SpringRestPactRunner
    import org.junit.Before
    import org.junit.runner.RunWith
    import org.springframework.beans.factory.annotation.Autowired
    import org.springframework.boot.test.context.SpringBootTest

    @RunWith(SpringRestPactRunner::class)
    @Provider("UserManager")
    @Consumer("Quoki")
    @PactFolder("../consumer/src/consumer/http/pacts")
    @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT,
            properties = ["server.port=8601"])
    class QuokiUserAuthenticatePactItTest {

        @Suppress("unused")
        @JvmField
        @TestTarget
        final val target: Target = HttpTarget(port=8601)

        @Autowired
        private lateinit var userRepository: UserRepository

        @Before
        fun setUp() {
            userRepository.deleteAll()
        }

        @State("user exists")
        fun userExists() {
            userRepository.save(QuokiUser(userName = "alice", password = "s3cr3t"))
        }
    } 
```

运行这个将提供结果

```
 Verifying a pact between Quoki and UserManager
    Given user exists
        a request to authenticate
    returns a response which
        has status code 204 (OK)
        has a matching body (OK) 
```

## 最佳实践

### 坏约定

一个坏的约定，规定了它所认为的无效用户名的前提条件，并与预期的错误消息紧密结合。

这是单元测试提供者的实现细节，而不是契约本身。因此，提供者无法在不破坏使用者的情况下更改验证规则或错误消息。

给定:爱丽丝不存在

**收到:**创建用户的请求，用户名包含除下划线以外的特殊字符

**响应:**是 400 坏请求

**响应正文:** { "错误":"用户名不能包含特殊字符" }

### 美好的约定

来自消费者的良好约定隐藏了提供者的实现细节。它必须从消费者的角度捕捉期望。在下面的例子中，消费者不应指定其认为无效的用户名。

给定:爱丽丝不存在

**收到:**用无效用户名创建用户的请求

**响应:**是 400 坏请求

**响应正文:**{ " error ":"[一个非空字符串]"}

### 结论

如果您有更多问题或需要更多信息，您可以在此找到所有信息