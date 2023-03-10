# 嘿，回应者👋，请给我一个 Symfony 的回复📜

> 原文：<https://dev.to/ro0nl/hey-responder--please-respond-to-me-with-a-symfony-response--4n1d>

当在一个典型的 Symfony web 应用程序中工作时，我们通常会查看它的控制器来发现应用程序可能遇到的任何危险信号。这里经常会发生很多魔法...

> 控制器是您创建的 PHP 函数，它从请求对象读取信息，并创建和返回响应对象。响应可能是 HTML 页面、JSON、XML、文件下载、重定向、404 错误或任何其他内容。控制器执行应用程序呈现页面内容所需的任意逻辑。([来源](https://symfony.com/doc/current/controller.html))

在这篇文章中，我想重点介绍如何生成 Symfony 的 HTTP 响应对象，该对象将从控制器返回以进行渲染。

Symfony 将继承`AbstractController`作为其最佳实践([来源](https://symfony.com/doc/current/best_practices/controllers.html))...等等什么？那有规模吗？

我猜 _(ツ)_/，今天你将继承 24 个实用方法。此外，您依赖于服务定位器来实现这一点(不是纯粹的依赖注入)。如果这对你有用，或者你真的不在乎；继续前进！

但是，如果你已经在使用[动作域响应者](https://en.wikipedia.org/wiki/Action%E2%80%93domain%E2%80%93responder)模式( *ADR* ，那么我有消息告诉你...认识一下 HTTP 响应者:【https://github.com/ro0NL/symfony-http-responder】T4

> 让 R 部分变得很有趣。
> —我

```
use ro0NL\HttpResponder\Bridge\Twig\Template;
use ro0NL\HttpResponder\Responder;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\HttpFoundation\Request;

class SomeHttpAction
{
    public function __invoke(Request $request, Responder $responder): Response
    {
        return $responder->respond(new Template('home.html.twig'));
    }
} 
```

我添加了一个[对照表](https://github.com/ro0NL/symfony-http-responder#comparison-table)来帮助你决定你是否真的需要`AbstractController`👍

下一步是添加一个`Kernel::VIEW`事件监听器，并根据控制器结果从那里做出响应。实际上，这意味着默认情况下你是无服务依赖的！

```
return new Template('home.html.twig');
return new Redirect('/path');
return new RouteRedirect('route_name'); 
```

或者更高级的例子:

```
return (new Template('some.html.twig'))
    ->withStatus(500)
    ->withHeader('X-Hello', 'world')
    ->withFlash('success', 'Ah yeah')
    ->withLink('href', ['rel']); 
```

干杯，