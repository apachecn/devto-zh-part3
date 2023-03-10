# 在 5 分钟内掌握反应状态和道具的艺术

> 原文：<https://dev.to/rleija_/master-the-art-of-react-state-and-props-in-5-minutes-3hao>

*原载@ Linguine 博客[T3】](https://linguinecode.com/post/master-react-state-and-props)*

工程师学习 React 的一个常见新手问题是，“道具和状态有什么区别？”或者谷歌“反应:道具 vs 状态”。

当我第一次开始时，我花了几个小时试图理解这种差异，我发现大多数文章都以错误的方式教授它。

大多数文章首先从学习 props 是什么意思开始，然后才是 React state。但我认为这是学习什么是反应状态和道具以及它们如何一起工作的错误方法。

所以我们先从了解什么是反应状态开始。

## 反应过来的状态是

让我们从定义什么是反应状态开始。

反应状态是一个对象。

反应状态对于其子组件可以是私有的或公共的。

反应状态可以保存影响反应组件输出的信息。

## 行动中的反应状态

[![](img/3e88456867b306d2f1bfac58d7bd2441.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8UoQ1CZk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/linguine-code-cdn/media/cat-app-comp.png)

我们看到的第一块代码是一个简单的 cat react 应用程序。这个卡特彼勒应用程序的目标是确定上面列出的每个定义。

现在让我们创建我们的反应状态。

[![](img/b8aef10f07d3a5c92013d474c027be89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZlfZYUpn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/linguine-code-cdn/media/cat-app-state.png)

就是这么简单！

但是这个空的状态对象是没有用的，直到我们开始向它添加一些数据。

因此，我们将向状态对象添加一个新属性(不是 React 组件属性),并将添加几个 JavaScript 事件来处理添加新猫名的操作。

[![](img/2472eb7e6d805df138f32efefbd5e0f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u3qznrG7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/linguine-code-cdn/media/cat-app-add-form-1.png)

好吧，事情变得很复杂！但我会很容易地给你解释清楚。

首先，我们的状态对象有两个新属性。

*nameOfNewCat，*会在您键入时为您的猫保存新名称。

*cats，*是另一个新属性，它将保存您的猫名列表。

在我们的 *render()* 方法中，我添加了一个输入标签，并将两个动作绑定到按钮和输入标签。

这一点很重要，因为每个动作( *handleChange* 和 *handleAddCatClick* )都在修改状态对象。

让我们中断 *handleChange* 动作，这样您就可以理解如何以正确的方式修改状态。

[![](img/249f11927b761ebe3b1099fc6faf900e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dIIwKtay--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/linguine-code-cdn/media/cat-app-input-change.png)

注意我们是如何使用 *this.setState()* 的。这是修改反应状态对象中任何属性的正确方法。

在传统的 JavaScript 中，您通常会直接修改对象。但这在实践中是一个很大的禁忌。

这是因为直接修改状态可能会导致修改被覆盖，导致应用程序不一致。

*setState* ，不直接修改而是创建一个待定状态转换。

另一个需要注意的重要事情是，当您更新 React 状态树时，它只修改第一级属性。

啊？？

看看下面的例子就明白我的意思了。

## 如何用 setState()更新嵌套状态对象

*附:下面的例子不是我们正在开发的 cat 应用程序的一部分。*

[![](img/3d83060fcbcc92b7ea0a47f990318e8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---Q_A4PoC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/linguine-code-cdn/media/cat-app-setState-nested-objects.png)

上图展示了一个状态对象的例子，它有名字、年龄、喜欢的狗和猫等属性。

如果您运行 *setState* 来更改 *name* 的值，那么 React 将保留已定义的其他值，只更改 *name* 属性。

您可能认为只有修改特定的属性才会改变，而其余的值会保持不变。但是如果你认为那是错误的。

如上图所示，如果我们将*猫*的 likes 更新为 false。你会看到状态对象已经移除了我们对*狗*的喜欢。我们都喜欢狗！

为了保持我们对狗的喜欢，我们必须告诉 *setState* 我们想要保持我们之前的嵌套值。

在 ES6 中，我们可以做一些叫做 spread 操作符的事情。正如你在最终结果中看到的，我们保留了对狗的爱，改变了对猫的爱。

## 反应状态影响输出

我们已经讨论了什么是反应状态，以及如何添加和更新反应状态数据。

您的下一个问题可能是，“我们如何显示保存在 state 中的数据？”

这很容易做到。

<figure>

[![](img/34fa3319b86848054cc991f520f2f097.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8KJFEMwk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/linguine-code-cdn/media/cat-app-display-cat-names.png)

<figcaption></figcaption>

</figure>

在我们的 cat 应用程序中，我们只需要从 React 状态对象中获取 *cats* 属性，并为每只猫呈现一些标记。

在上图中，您将使用。 *map()* array 方法创建一个新的 *li* 标签，输出我们输入的猫的名字。

输出应该如下所示。

[![](img/d44fed0e985281afda95f88048915f16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AjpBGAFo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/linguine-code-cdn/media/cat-app-cat-name-output.jpg)

到目前为止，我们已经构建了一个利用状态来保存和显示数据的 React cat 应用程序。

现在，我们必须了解 React props 如何很好地处理状态。

## React 道具的定义

让我们从定义什么是反应道具开始。

反应道具是描述我们应该看到什么的输入。

[![](img/32421f34d8aca68a8afaac3e0fe12bf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MG-fEPgr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/linguine-code-cdn/media/cat-app-input-field.png)

上面是一个普通文本字段的例子，我插入了一个类型为*的输入*来等于**文本**。

如果我添加另一个名为*占位符*的输入，它将使我的输入字段对用户来说看起来不同。

[![](img/c8c0cf5a534675bc54f743ea842498b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yDyEnJDr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/linguine-code-cdn/media/cat-app-input-field-placeholder.png)

*输入*等于**道具**。

它与 React 组件的工作方式相同。让我们来看看。

## 传递状态数据作为道具

在同一个文件中，您可以创建一个名为 Cat 的新 React 组件。猫将期待一个名为*的道具命名为*。

[![](img/c1877be345d0944429b69e47f7f6b5e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YzLi82bs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/linguine-code-cdn/media/cat-app-cat-component.png)

现在，您将更新 CatApp 组件中显示猫名列表的代码。

[![](img/5e9f55f5ac427b6689b72fabbec929d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yikqx1bP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/linguine-code-cdn/media/cat-app-using-cat-comp.png)

您在这里所做的只是传递我们存储在我们的*状态*对象中的猫名，并将其作为*属性*传递给您创建的猫组件。

然后，Cat 组件将显示输入的名称，并在一个列表项 HTML 标记中显示该名称。

## 结论

理解反应状态和道具看起来很有挑战性，有些令人困惑。

但是按照正确的顺序学习可以帮助你更快更好地理解它。

状态是包含关于组件的私有或公共本地数据的对象。并且它可以用于影响组件的输出。

道具只不过是描述你应该看到的东西的输入定义。