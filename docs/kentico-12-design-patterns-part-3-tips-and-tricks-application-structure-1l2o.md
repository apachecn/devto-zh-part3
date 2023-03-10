# Kentico 12:设计模式第 3 部分-提示和技巧，应用程序结构

> 原文：<https://dev.to/seangwright/kentico-12-design-patterns-part-3-tips-and-tricks-application-structure-1l2o>

<figure>[![](img/85789ba3c0948ee52c26094c05c3ba7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vboLon5z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AHxZnLS5msoiX8mxB) 

<figcaption>照片由[阿齐兹](https://unsplash.com/@acharki95?utm_source=medium&utm_medium=referral)上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

## 提示和技巧:应用程序结构

自 2018 年 11 月以来，两个世界发生了巨大的碰撞——ASP.NET MVC 和肯蒂科 CMS。

现在，当在 Kentico CMS 中存储和组织内容时，Kentico 开发人员甚至有更多的能力来定制向站点访问者交付内容的方式。

有了这个新的定制空间，就有机会确定有用的模式和最佳实践。

下面我列出了五个技巧和窍门，在与 Kentico 12 集成时，你可以用它们来帮助构建和组织你的 MVC 代码库。👍🏽

## #1 —提示:特征文件夹

在创建新的应用程序、库或测试项目时，您可能已经多次使用 Visual Studio " ***"文件- >新建项目*** "向导。

向导生成的代码很有帮助，因为它可以让您立即投入工作。不幸的是，这段代码只是为了介绍如何选择。NET 项目工程。

当向您的解决方案添加新的类库项目时，有人保留向导为您创建的`Class1.cs`文件吗？我认为不是。😒

类似地，当我们创建新的 ASP.NET MVC 5 项目时，向导给了我们一个可预测的文件夹结构，旨在引导我们进入 MVC 范式，指出与以前的 ASP.NET 解决方案(WebForms)相比所有新的重要特性。

这意味着，基于类的组织，最初的焦点是框架(ASP.NET MVC)而不是你的应用程序的特性。😑

下面是 Visual Studio 的“ ***文件- >新建项目*** ”对话框创建的默认文件和文件夹的例子:

<figure>[![](img/fb4767181d3c3efb264d95569e2ef960.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xFejxGW---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/577/1%2AcFWjilWaeGrqPGVaExuGvg.png) 

<figcaption>这些就是我们最终拥有的文件- >新项目- > MVC</figcaption>

</figure>

这对于一个演示应用程序来说是可以的，但是当我们向客户销售 Kentico CMS 解决方案时，我们不是在构建演示——我们是在用*的*业务规则和定制内容构建*他们的*站点。💪🧠

简而言之，不要遵循项目模板为您设计的模式，因为有更好的选择——特别是当您的应用程序在范围和复杂性上增长时。

取而代之的是，采用一种“特性文件夹”的组织方法，将应用程序的特性放在前面和中心，并将框架实现细节视为实现细节。👍🏽

> 我已经谈到了“框架特性反模式”，我肯定不是第一个谈论这个话题的开发人员。查看[我的演讲](https://docs.google.com/presentation/d/1AXx8f5OuMYfQaqErqbsziRcKsWH9ojoYT6n358b34fY/edit?usp=sharing)中的幻灯片作为参考，或者查看“ [MVC5 功能文件夹](https://duckduckgo.com/?q=mvc5+feature+folders&t=ffab&atb=v144-1&ia=web)的搜索结果。

下面的截图展示了我为您的新 Kentico 12 MVC 应用程序推荐的组织。

<figure>[![](img/9d0867ecc6dcda19ec9c18d2d6da9432.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qzLSWZpx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/548/1%2A7Vb2kclKxLSzFLHFj1ITXQ.png) 

<figcaption>一个基于“特征文件夹”组织的类</figcaption>

</figure>

我非常相信这种模式，所以我也推荐它用于通用类库和单元测试项目。👊

上面的例子并没有完全“聚焦于特性”,因为所有 Razor 视图文件仍然在`\Views`文件夹中。我相信这些也应该与特性类型共存—例如)`Kentico12WebMVC\Features\Products\Detail.cshtml`，但是 Kentico MVC 集成库还不支持这种方法。

> 我希望 [Kentico 12 MVC service pack](https://www.kentico.com/blog/kentico-12-service-pack-news) 包含对 MVC 视图引擎定制的支持——特别是对视图路径约定的支持，这样我们就可以在项目中真正关注“特性”。
> 
> 你可以在 Kentico 的 UserVoice 网站上为这个功能[投票。让肯帝科知道我们想要这个！](http://ideas.kentico.com/forums/239189-kentico-product-ideas/suggestions/36877402-official-support-for-feature-folders-pattern-in)

## #2 —技巧:页面生成器类作为特性

一旦你开始用“特性文件夹”的方法组织你的 MVC 项目，你可能会想，“我应该把我的 Kentico 小部件、InlineEditor 和 Section 类放在哪里？”🤔

答案很简单。把他们当特色！

如下所示，我创建了一个`PageBuilders`特征文件夹，并将我所有的类型放在那里。

辣酱！🔥

<figure>[![](img/7d1494560c0ed0d07d6a1700e915394c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zr3Y2KRq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/597/1%2AncoC5myLjAdk5pi84I1xIg.png) 

<figcaption>页面生成器类型为特征文件夹</figcaption>

</figure>

## #3 —提示:复数名称空间和单数类型名称

您可能偶尔会遇到的一个问题是 C#中命名空间和类型之间的冲突。当对您的类型使用基于“特征文件夹”的组织方法时，此问题可能会更频繁地出现。

例如，如果我有一个名称空间`App.Product`，并且在这个名称空间中我有一个类`Product.cs`，那么无论我在哪里尝试使用这个类型，我都会收到一个编译器错误。😩

`var product = new Product();`产生`'Product' is a namespace but is used like a type.`

避免这种情况的最简单方法是遵循一个简单的约定:

> #### *plural namespace, singular type name*

正如你在上面的屏幕截图中看到的，我已经将所有的文件夹名称复数化，因为 C#项目中的文件夹名称会为其中包含的类型生成名称空间。

有时你会以一些稍微奇怪的文件夹名结束(例如。把你的`HomeController`放到一个名为`Homes`的文件夹中。如果你不喜欢这样，试着想一个更具描述性的文件夹/功能名称，或者考虑到上述问题的警告，破例使用一个单一的文件夹名称。

> 我建议使用这种“复数文件夹，单数类型”的约定，即使您在代码库中没有遵循“特性文件夹”的组织。

## #4 —招数:分布式路由配置

关于 ASP.NET·MVC5，本·福斯特写了很多很棒的文章,如果你正在寻找一些解决方案来帮助你的 MVC 代码库扩展并利用 MVC 5 提供的所有强大功能，我推荐他的博客作为一个很好的起点。✨🌟

他推荐的一个我很喜欢的模式是[分布式路由配置](https://benfoster.io/blog/improving-aspnet-mvc-routing-configuration)。我认为它在“功能文件夹”组织中非常有效。

您将在网上看到的最常见的路由配置模式包括在一个文件中注册您的所有路由—通常是`RouteConfig.cs`。