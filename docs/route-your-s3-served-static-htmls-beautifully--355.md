# 路由您的 S3 服务的静态 HTMLs 漂亮

> 原文：<https://dev.to/marton_veto/route-your-s3-served-static-htmls-beautifully--355>

如果你是一个不喜欢玩游戏的开发者，并且你没有一个好的反应，Angular 或者*在这里插入任何一个好的前端框架，当你阅读这篇文章的时候这个框架很流行*站点，它支持客户端路由，但是你有一些静态的 HTML 文件(不管是什么原因)并且你想在 AWS S3 托管它，这篇文章是为你准备的！

我将向你展示如何通过 S3 做一些基本的路由。你的用户不用加丑*。如果他们想到达 */products.html* 页面，可以在请求的末尾添加 html* 后缀，但是他们可以输入 */products* 。

🙀🙀🙀

多酷啊。打字更少，收入更多！来实施吧！

但是首先，无聊的任务:

*   用默认设置在 S3 创建一个桶。我们以后会调整一切。把你的静态文件上传到桶里(或者[矿](https://github.com/mpxr/s3-routing))。

## 配置权限

1.  为了让您的客户访问网站端点的内容，您必须调整存储桶的公共访问设置。

    *   进入**权限** > **公共权限设置**
    *   点击编辑，确保以下两个选项**未被选中**:
        *   *阻止新的公共存储桶策略*:这允许您将新的公共策略添加到存储桶中。我们将在下一步中这样做。
        *   *如果 bucket 具有公共策略，则阻止公共和跨帐户访问*:这允许用户访问公共 bucket 的内容。如果设置了此选项，则只有存储桶的创建者和其他 AWS 服务可以访问此存储桶。这是 2018 年末)的一个非常新的特点。
2.  现在，我们能够向我们的存储桶添加存储桶策略。

    *   转到**权限** > **斗策**
    *   添加下面的策略文档，它允许您从存储桶中请求每个对象:

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Effect":  "Allow",  "Principal":  "*",  "Action":  "s3:GetObject",  "Resource":  "arn:aws:s3:::<YOUR_S3_BUCKET_NAME>/*"  }  ]  } 
```

## 配置静态网站托管

现在我们已经设置好了所有的权限，让我们开始有趣的部分吧！假设我有以下 HTML 页面:

*   *index.html*，这是我的登陆页面
*   *about.html*，*products.html*
*   还有*error.html*，这是我的错误处理页面

我想要以下路由集:

*   `/`->-`index.html`
*   `/about`->-`about.html`
*   `/products`->-`products.html`
*   其他一切-> `error.html`

第一条也是最后一条规则很容易设置，因为 S3 通过*静态网站托管*功能支持它。在你的页面上进入**属性** > **静态网站托管**。将*索引文件*字段设置为 index.html，将*错误文件*设置为 error.html(或者您给这些文件起的名字)。现在是更棘手的部分:你必须设置一些重定向规则。

它是通过 XML 配置的...😬

但也没那么糟糕。请原谅我。

你必须建立*路由规则*，它包括两件事:

*   一个*条件*在这里我们提供我们想要捕捉什么样的请求，例如`/products`
*   并且该请求将被*重定向到*，例如到`/products.html`。

您将拥有一个 *RoutingRules* 父节点和与您想要重定向的 HTML 站点数量一样多的 *RoutingRule* 。这就是如果我把路由添加到我的两个静态文件中的最终效果:

```
<RoutingRules>
  <RoutingRule>
    <Condition>
      <KeyPrefixEquals>about</KeyPrefixEquals>
      <HttpErrorCodeReturnedEquals>404</HttpErrorCodeReturnedEquals>
    </Condition>
    <Redirect>
      <ReplaceKeyWith>about.html</ReplaceKeyWith>
    </Redirect>
  </RoutingRule>

  <RoutingRule>
    <Condition>
      <KeyPrefixEquals>products</KeyPrefixEquals>
      <HttpErrorCodeReturnedEquals>404</HttpErrorCodeReturnedEquals>
    </Condition>
    <Redirect>
      <ReplaceKeyWith>products.html</ReplaceKeyWith>
    </Redirect>
  </RoutingRule>
</RoutingRules> 
```

其中最重要的部分是`HttpErrorCodeReturnedEquals`元素。当你需要一个带有关键字“产品”的 S3 桶中的资源时，S3 找不到它，因为它不存在(显然我们的桶中只有 HTML 文件),它会返回一个 404(未找到)错误，这个错误会传播到客户端，我们甚至不会访问*重定向*规则。

仅此而已。但是不要忘记，最终在你的 URL 中，你的用户仍然会看到他们浏览的是`products.html`而不是`/products`。但是这将减轻他们的用户体验，因为他们不需要在每次请求后都输入`.html`。

### 路由到外部站点

使用 S3 路由规则，您可以将您的请求路由到外部网站。如果您的用户从您的页面请求`/facebook`资源，您可以将他们重定向到您的脸书页面，[https://facebook.com/YOUR_FACEBOOK_ACCOUNT_POSTFIX](https://facebook.com/YOUR_FACEBOOK_ACCOUNT_POSTFIX)。你可以这样设置规则:

```
<RoutingRule>
  <Condition>
    <KeyPrefixEquals>facebook</KeyPrefixEquals>
    <HttpErrorCodeReturnedEquals>404</HttpErrorCodeReturnedEquals>
  </Condition>
  <Redirect>
    <HostName>facebook.com</HostName>
    <ReplaceKeyPrefixWith>/YOUR_FACEBOOK_ACCOUNT_POSTFIX</ReplaceKeyPrefixWith>
  </Redirect>
</RoutingRule> 
```

## Postscript

我希望这篇文章能帮助你用 S3 做基本的路由。当我不得不做同样的事情时，我花了几个小时来寻找最佳解决方案。我希望你不必在这上面花那么多时间。你可以在 Git [repo](https://github.com/mpxr/s3-routing) 中找到我上面粘贴的每一段代码。

如果你发现上面写的东西有任何错误，请在评论区公开指责我。或者如果这对你有帮助，只要说声“谢谢”就行了。我会很感激的。

再见。👋