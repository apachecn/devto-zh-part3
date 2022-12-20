# 使用 Spring WebTestClient 发送多部分表单数据

> 原文：<https://dev.to/shavz/sending-multipart-form-data-using-spring-webtestclient-2gb7>

(x-转自个人博客[此处](https://www.shiveenp.com/2019/04/21/Sending-Multipart-Form-Data-Using-Spring-WebTestClient-md/))

## 背景

在过去的一年多时间里，我一直在广泛地使用 spring，尤其是 spring webflux 为我们的客户构建可扩展的反应式微服务。

从 spring MVC 开始，学习 webflux 并习惯于反应式编程通常是一个很好的值得学习的经历，如果你没有听说过反应式编程和/或一直在考虑尝试一下并且不知道从哪里开始，我强烈建议你浏览一下[参考资料](#References)部分。但本质上，反应式编程涉及一种以可控(从消费者的角度来看)和非阻塞的方式创建、请求和操作数据的模型。

[WebTestClient](https://docs.spring.io/spring/docs/current/spring-framework-reference/testing.html#webtestclient) 是一个带有流畅断言的反应式测试高级 http 客户端，封装在 spring web flux 中。最近，在集成测试一个接受数据为[多部分/形式数据](https://tools.ietf.org/html/rfc7578)的应用程序时，我必须弄清楚如何使用 webtestclient 有效地测试数据，并且我个人发现互联网上缺乏全面的资源，所以我写了这篇博客来分享我自己的学习。

## 用 Webflux 进行 Web 表单测试

假设我们试图发送请求来填充接受文档(图像、文本、普通二进制文件等)的表单 api。)和一些文字资料。

为了帮助我们的示例，让我们假设表单是一个文档共享服务的配置文件设置，并接受以下输入:

*   配置文件图像( *api 标签:配置文件图像*
*   用户名( *api 标签:用户名*)
*   电子邮件( *api 标签:电子邮件*)
*   要共享的 PDF 文档( *api 标签:userDocument* )

为了开始发送数据，我们将使用名为 [MultipartBodyBuilder](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/MultipartBodyBuilder.html) 的 spring 库，它为设置多部分请求的主体提供了一个很好的 api。

要发送第一部分，我们可以将配置文件图像设置为:

```
val bodyBuilder = MultipartBodyBuilder()

bodyBuilder.part("profileImage", ClassPathResource("test-image.jpg").file.readBytes()).header("Content-Disposition", "form-data; name=profileImage; filename=profile-image.jpg") 
```

Enter fullscreen mode Exit fullscreen mode

为了解释一下这里发生了什么，我们只是告诉 body builder 上传一张在`src/test/resources`文件夹中找到的名为`test-image.jpg`的图片，作为这个身体的轮廓图片部分。这里真正的问题是设置**头**部分，因为这是 webtestclient 内部使用的部分(特别是 webflux 内部使用的 [Synchronoss-nio](https://github.com/synchronoss/nio-multipart) 库)来确定发送的表单数据的类型以及如何处理它。

另外，请注意，将在接收请求的 web 服务器中上传的真实文件名是作为头的一部分发送的`profile-image.jpg`文件名。

类似于概要文件图像，我们也可以发送整个请求有效负载的文档部分:

```
bodyBuilder.part("userDocument", ClassPathResource("user-document.pdf").file.readBytes()).header("Content-Disposition", "form-data; name=userDocument; filename=my-thesis.pdf") 
```

Enter fullscreen mode Exit fullscreen mode

与之前的有效载荷相似，我们测试了车身生成器💪以字节形式读取测试资源文件夹中名为`user-document.pdf`的文件，并将名为`my-thesis.pdf`的文档发送到表单 web api。

正如你已经看到的，与其他一些方法相比，比如在这个[优秀的博客](https://www.baeldung.com/spring-rest-template-multipart-upload)中，使用多体健美运动员是相当方便的。

现在，对于表单 api 的最后两部分，它们通常只是纯文本，我们可以将它们设置为:

```
bodyBuilder.part("username", "shiveenpandita", MediaType.TEXT_PLAIN).header("Content-Disposition", "form-data; name=username").header("Content-type", "text/plain")

bodyBuilder.part("email", "shiveenpandita@gmail.com", MediaType.TEXT_PLAIN).header("Content-Disposition", "form-data; name=email").header("Content-type", "text/plain") 
```

Enter fullscreen mode Exit fullscreen mode

呜哇！🎉我们现在已经连接了所有的表单域。

现在，为了查看所有操作并把它们放在一起，我们可以简单地设置一个 spring 集成测试，并使用我们新设置的 body builder 作为:

```
@RunWith(SpringRunner::class.java)
@SpringBootTest
@AutoConfigureWebTestClient
class WebClientTest {

    private lateinit var webclient: WebTestClient

    @Test
    fun `test webform api`() {
        val bodyBuilder = MultipartBodyBuilder()

        bodyBuilder.part("profileImage", ClassPathResource("test-image.jpg").file.readBytes()).header("Content-Disposition", "form-data; name=profileImage; filename=profile-image.jpg")

        bodyBuilder.part("userDocument", ClassPathResource("test-document.pdf").file.readBytes()).header("Content-Disposition", "form-data; name=userDocument; filename=my-thesis.pdf")

        bodyBuilder.part("username", "shiveenpandita", MediaType.TEXT_PLAIN).header("Content-Disposition", "form-data; name=username").header("Content-type", "text/plain")

        bodyBuilder.part("email", "shiveenpandita@gmail.com", MediaType.TEXT_PLAIN).header("Content-Disposition", "form-data; name=email").header("Content-type", "text/plain")

        webClient.post()
            .uri("/v1/test-api")
            .contentType(MediaType.MULTIPART_FORM_DATA)
            .body(BodyInserters.fromMultipartData(bodyBuilder.build()))
            .exchange()
            .expectStatus().isOk
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段将成功地向我们的测试 api 发送所需的数据，并且 webtestclient 断言响应为 200 OK。

[![](img/5cfb7f5feaa6eee1f742d59a02f0b81c.png)](https://i.giphy.com/media/l0ErKDci4GgPkcAF2/giphy.gif)

## 参考文献

*   [反应宣言](https://www.reactivemanifesto.org/)
*   [春网流量](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#spring-webflux)
*   [牛逼榜](https://github.com/lucamezzalira/awesome-reactive-programming)