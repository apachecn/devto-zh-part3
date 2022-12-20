# Kentico 12:设计模式第 1 部分——编写可测试代码

> 原文：<https://dev.to/seangwright/kentico-12-design-patterns-part-1-writing-testable-code-258a>

<figure>[![](img/4f3ba4c9fd2b8239712d74a6db1a0706.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wmfYywB5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AxpmHwds5Fbvdv7PA) 

<figcaption>照片由[尼古拉斯·托马斯](https://unsplash.com/@nicolasthomas?utm_source=medium&utm_medium=referral)于[上](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

## 好:我们为什么爱 Kentico😻

在我看来，Kentico 的设计一直是为了让它的用户富有成效。对于传统的门户引擎拖放式 UI 生成器和。开发人员可以使用. NET 库与 CMS 进行交互。

需要从数据库中获取用户数据吗？用`UserInfoProvider.GetUsers();`调用静态提供者类。✔️

您需要知道当前请求运行在哪个站点下吗？使用静态上下文访问器`SiteContext.CurrentSiteName;`。✔️

想要确保作为类型对象可访问的一些数据实际上是您最初存储的 int 值吗？将其包装在静态实用方法调用`ValidationHelper.GetInteger(originalData, 0);`中。✔️

关于所有这些调用，需要注意的关键点是它们通常是在`static`类上的`static`方法。

这些类不需要实例化，我们不需要理解它们的依赖图，它们是预先打包好的，随时可以使用，只要我们能够访问部署它们的`.dll`,它们就是真正的全局值。

这种设计的含义是，开发人员可以使用的所有上述工具，用于在 Kentico CMS 上构建复杂的网站，可以尽可能简单地被发现和利用。🙌

## 坏:不可告人的秘密💀

全球化的快乐世界也有黑暗的一面。

这些类型通常与它们的依赖项紧密耦合，有些只能在特定的上下文中使用(通过 ASP.NET 请求管道的实时请求)，并且很难(如果不是不可能的话)进行单元测试。😱

> 从这里开始，我将提到“可测试”或“不可测试”的东西，我指的是单元测试。

这一后果并不完全是 Kentico 的错。

ASP.NET Web Forms 不是一种鼓励测试或分离架构的技术。它旨在确保 Windows 窗体桌面开发人员在构建 web 应用程序时获得最佳的工作效率。

Kentico 建立在 web 表单上，因此将模仿它的模式和优先级——易用性，抽象出 web 开发的“Web”部分，以及访问静态的“环境上下文”全局变量，如`HttpContext`。

> 如果你以前从未听说过环境上下文，你可以在这里阅读[设计模式。需要注意的一点是在“结果”部分，其中声明“使用环境上下文对象的系统的结果将取决于问题域”。这里的“问题域”相当于“真正运行的 ASP.NET 站点处理一个主动 HTTP 请求”。](https://aabs.wordpress.com/2007/12/31/the-ambient-context-design-pattern-in-net/)

但是现在我们开始了 Kentico 开发的新时代，我们的代码将在 Web Forms CMS 架构和更新的 MVC 架构中运行。

MVC 被设计成分离关注点，将声明性 UI 从过程逻辑中分离出来，并允许像控制反转、依赖注入和组合这样的模式，而不是继承。🤓

作为开发人员，我们如何在 Kentico 提供的用于集成 CMS 的强大的库和工具集与 MVC 允许(和鼓励)的最佳实践架构模式之间架起一座桥梁？🥺

> 并非 Kentico 的所有课程都有这些问题。例如，`ValidationHelper`是静态的，但基本上也是一个纯函数，它试图将类型`object`的值转换为特定的类型。它可以安全地用在代码中的任何地方，并且在运行时会给出与测试时相同的结果。

## 但是等一下，还有希望！🤗

让我们看一个简单的例子，如何封装传统 Kentico 应用程序中方便但不可测试的部分。

对于本例，我们将查看如何访问 Kentico 的设置 API，该 API 允许进行配置，通常存储在一个站点的`web.config`中的`<appSettings>`中，然后存储在 Kentico 的数据库中。

您通常如何获取当前请求站点的数据？🤔可能是以下:

```
string settingValue = SettingsKeyInfoProvider.GetValue("someKeyString", SiteContext.CurrentSiteName); 
```

虽然 Kentico 确实为我们提供了一种在单元测试中使用`Fake<>`来测试`SettingsKeyInfoProvider.GetValue()` [的方法，但是却没有办法对`SiteContext`进行单元测试。](https://docs.kentico.com/k12/custom-development/writing-automated-tests/faking-info-and-provider-objects-in-unit-tests)

`SiteContext`和所有其他方便的静态`***Context`类型都是不可测试的。为了正确工作，它们需要一个真实的 ASP.NET 应用程序和一个实时请求，因为它们是静态的，所以我们不能模仿它们。😒

我们如何构建自己的 API 来提供对这些数据的访问，但又不依赖于`SiteContext`？

## 介是来帮忙的

下面的示例代码介绍了我们的第一个也是最好的工具，它使我们的应用程序更易测试:接口。

在 C#中，接口不能由`static`类来实现，所以这立即使我们免于陷入我们已经发现自己所处的境地。

但是它们也提供了一个接口，我们可以在需要的时候插入对 Kentico 的`static`类和方法的现有调用，而不用暴露实现。没必要把孩子和洗澡水一起倒掉。我们不想重写 CMS 我们只是希望它是可测试的。😉