# 在代码中分离职责

> 原文：<https://dev.to/flippedcoding/separating-responsibilities-in-code-23i>

我们都知道，当我们开始编程时，你应该保持你的文件干净。只在 CSS 文件里写 CSS，不要在你的 HTML 文件里写 JavaScript 之类的。然后我们变得更先进。

在代码中分离责任不仅是一个最佳实践，当您需要找到问题的根源时，它将有助于保持您的理智。有相当多的具体原因让你想使用这种做法，这里是其中的几个

测试变得更容易管理，因为您可以跟踪调用堆栈来找到问题的根源。当你试图找出你需要关注的是前端还是后端时，这尤其有用。当您必须调试代码时，在一个文件中修改几行代码比在多个文件中更新同一行代码要好得多。

说到这里，分离职责也有助于维护您的代码库。当您有一个特定的文件夹来保存 API 输出所需的所有自定义类型时，您可以添加一个新变量，而无需翻遍文件。不需要进入项目中的每个文件，就可以改变整个站点的布局。这有助于你做出任何项目中出现的或大或小的改变。

然后是代码依赖和注入的好处。您将能够看到并控制代码值在哪里被使用，这些值来自哪里，以及它们是如何被处理的。如果数据库中有需要绑定到 HTML 元素的更新数据，代码分离可以让您安全地处理这些更改。

拥有独立的职责使您可以限制前端拥有的后端访问权限。通过使用特定的 API 调用在数据库值到达前端之前对其进行处理，可以保护用户信息。您可以更深入地了解代码分离。

在 API 调用内部，您可以更改您编写的函数的访问级别。您也可以在 JavaScript 的前端使用相同的访问修饰符来实现这一点。你可以拥有不能在类外调用的私有函数。最常用的是你的公共函数，它们可以在类实现的任何地方被调用。

职责分离可以用一种特定于项目的方式来处理。您可以简单地将每种语言保存在不同的文件中，也可以复杂地为您做的每一件事情编写不同的文件。请记住，职责分离也给了您一定程度的抽象。确保你没有在实际的代码和表面之间放置太多的层。

这是一门微妙的艺术，你会随着时间的推移逐渐学会，但有时你并没有意识到它正在发生。当你在考虑如何划分这些责任时，试着从长远和全局考虑。如果你看到一个很大的特性或者一些将来可能需要扩展的东西，那就去做好计划。

了解代码职责的分离是您更好地理解 web 项目整体架构的方法。一旦你学会了何时划分职责，并且知道你为什么需要这样做，你就离令人垂涎的高级 web 开发人员的身份更近了一步。

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)