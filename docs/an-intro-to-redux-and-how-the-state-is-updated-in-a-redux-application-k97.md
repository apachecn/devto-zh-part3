# 介绍 Redux 以及如何在 Redux 应用程序中更新状态

> 原文：<https://dev.to/aimenbatool/an-intro-to-redux-and-how-the-state-is-updated-in-a-redux-application-k97>

<figure>[![](img/aa0cb2244a8a29c7bbc3a9fd423a46fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q2x8GleG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_BSX61CxShyqW7oT7Kgc8Q.jpeg) 

<figcaption>照片由[费边摸索](https://unsplash.com/photos/XMFZqrGyV-Q?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/search/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

几天前我开始学习 Redux，它对我来说是一个压倒性的概念。在通过制作一个[个人书籍阅读应用](https://github.com/aimenbatool/my-reads)来完善我在 ReactJS 中的技能之后，我前往 Redux 了解更多。

今天我要分享几个不使用任何视图库(React 或者 Angular)的核心 Redux 概念。这是一种供将来参考的个人笔记，但它也可以帮助其他人。

我们一起来挖吧！

### Redux 是什么？

Redux 是一个开源库，用于提高 JavaScript 应用程序中状态的可预测性。这是一个独立的图书馆。它通常与 React 和 Angular 等其他库一起使用，以便更好地管理应用程序的状态。Redux 由 Dan Abramov 在 2015 年创建，以高效的方式处理复杂的状态管理。

当应用程序变大时，管理状态和调试问题变得更加困难。跟踪状态在何时何地发生了变化，以及需要在何处反映这些变化，这成为一个挑战。有时用户输入会触发一些 API 调用来更新一些模型。该模型反过来更新某个状态，或者可能更新另一个模型，等等。

在这种情况下，跟踪状态变化变得很困难。发生这种情况主要是因为没有定义规则来更新状态，并且状态可以从应用程序内部的任何地方进行更改。

Redux 试图通过提供一些简单的规则来更新状态以保持其可预测性，从而解决这个问题。这些规则是 Redux 的组成部分。

### Redux Store:

正如我们前面讨论的，Redux 的主要目的是在我们的应用程序中提供可预测的状态管理。Redux 通过拥有一个单一的真实来源，即一棵**单一状态树**来实现这一点。状态树是一个简单的 JavaScript 对象，它保存了应用程序的整个状态。与国家互动的方式只有几种。这使得我们很容易调试或跟踪我们的状态。

我们现在只有一个主状态，它占据了位于单个位置的应用程序的整个状态。对状态树的任何更改都会反映在整个应用程序中，因为这是应用程序的唯一数据源。这是 Redux 的第一个基本原则。

#### 规则 1 — [单一真理来源](https://redux.js.org/introduction/three-principles#single-source-of-truth)

> 整个应用程序的状态存储在单个存储区的对象树中。—正式文件

与状态树交互的方式有:

*   获取状态
*   倾听国家的变化
*   更新状态

一个**存储**是一个单独的单元，它拥有**状态树**和**方法**来与状态树交互。除了通过这些给定的方法之外，没有其他方法可以与存储内部的状态进行交互。

[![](img/6856204afae60954ac24dc4aef07d6a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Iau8lSPR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7qEb23-ElppRV2eU8Yv4gg.png)

我们来谈谈商店给我们提供的与状态交互的方法。

*   getState() —返回应用程序的当前状态。
*   dispatch(action)-更新状态的唯一方法是通过调度操作，dispatch(action)可以满足这一目的。我们稍后会更详细地讨论。
*   subscribe(listener) —此方法的目的是侦听状态变化。每次状态改变时，它都会被调用并返回更新后的状态。
*   replace reducer(next reducer)-替换存储当前用于计算状态的缩减器。

现在，当我们有一个包含状态树和一些与状态交互的方法的存储时，我们如何更新应用程序状态呢？

### 更新应用程序中的状态:

更新状态的唯一方法是分派一个动作。这是第二条规则。

#### 规则#2 — [状态为只读](https://redux.js.org/introduction/three-principles#state-is-read-only)

动作是一个普通的 JavaScript 对象，用来跟踪应用程序中发生的特定事件。使它特别的是一个“类型”属性，这是它的一个必要部分。

```
{
 type: "ADD\_BOOK\_TO\_THE\_CART"
} 
```

这个属性的主要目的是让 Redux 知道正在发生的事件。这种类型应该是对动作的描述。除了“类型”属性之外，它还可以包含有关正在发生的事件的其他信息。

动作可以包含任意多的信息。一个好的做法是尽可能少提供必要的信息，最好是 id 或任何唯一的标识符。

这里我们有一个向购物车添加一本书的操作。