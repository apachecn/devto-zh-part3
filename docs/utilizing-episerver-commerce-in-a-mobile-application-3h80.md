# 在移动应用中利用 Episerver 商务

> 原文：<https://dev.to/debpu06/utilizing-episerver-commerce-in-a-mobile-application-3h80>

过去，我们有几个客户向我们提出，希望能够在移动环境中为他们的团队成员提供 Episerver 内容。这方面的用例往往是事件、贸易展览或会议。在这些场景中，需要共享信息，通常是在短时间内，并且不在计算机前。我们认为实现这一目标的最佳方式是通过移动应用程序。我们可以开发应用程序，从 Episerver 提供现有内容，然后展示给潜在客户。

从这一点来看，合理的进展是销售。Episerver 为交易型电子商务网站提供了一个令人惊叹的框架，但这不是您可以使用它的唯一方式。我们意识到，如果我们更新客户互动，直接从互动转向销售，而不是从互动转向跟进，我们可以为客户提供更多价值。因为我们的客户已经通过他们的 Episerver 网站销售他们的产品，所以我们也想把它用于他们的移动应用程序。

## 我们的移动应用设置

为了方便最终用户(包括客户和顾客)使用，我们想创建一个 iPad 应用程序。我们认为拥有一个实际的应用程序比在浏览器中浏览网站会更干净。此外，它还会阻止用户离开客户端的内容。

我们通过创建一个包含 web 视图的移动应用程序实现了这一点。我们在 Episerver 中指定了页面树的一部分，专门用于应用程序。这一部分不会公开，我们可以将 web 视图指向这一部分的“起始页”。

通过响应式设计，隐藏 safari 的导航组件，并在 Swift 中配置应用程序以忽略站点域外的导航请求，我们能够创建原生应用程序体验。

从那里它只是一个问题，创建页面来处理查看产品，并检查。

## 听起来很简单，那么挑战是什么呢？

这种方法的问题是用户体验(UX)。因为我们希望这是一种引导式的体验，客户可以带着客户浏览他们的产品，所以我们不希望用户需要填写标准的网络信用卡表单。我们也不希望他们必须将自己的卡交给我们的客户，以便为他们输入信息。

## 解

解决办法是使用你看到的其他 iPad 用来交易的东西。一个[方形](https://squareup.com/us/en)读卡器。这将限制客户需要输入的数据量(姓名、电子邮件等)，而不要求他们输入所有的卡信息。这也将对原生应用的“感觉”有更大的贡献。

为了实现这一点，我们必须解决一个相当大的约束。在使用 Square reader 时，需要通过实际的 iOS 应用程序与之进行交互。这意味着，如果用户刷卡，我们就无法访问我们网站上通过 web 视图提供的数据。

幸运的是，Swift 有办法做到这一点。Swift 能够通过 web 视图调用 JavaScript 函数。您还可以在 web 视图中通过 JavaScript 调用之前在 Swift 中注册的功能。这允许信息从结账页面流向 JavaScript、应用程序、Square，然后以相反的顺序返回。Square 出于安全目的不传递信用卡数据。所以他们传递一个所谓的随机数。一旦我们收到这个 nonce 值，我们就可以通过 Episerver 完成结账过程，并使用这个 nonce 值来确认与 Square 的交易。

## 结果

对于我们的客户和我们的合作伙伴来说，这是非常强大的。有了我们的响应式优先开发理念和 iOS 应用程序框架，我们可以为我们的任何客户开发一个移动购买应用程序。

你怎么想呢?在移动环境下，你有没有销售 Episerver 网站产品的潜在案例？我们一直在寻找新的方法来扩展我们的解决方案，所以请随时给我们发送任何建议或想法。