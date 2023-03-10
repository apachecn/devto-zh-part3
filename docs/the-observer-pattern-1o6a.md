# 观察者模式

> 原文：<https://dev.to/joelsaupe/the-observer-pattern-1o6a>

观察者模式是软件工程中的一种设计模式，它允许多个地方之间的通信。在 web 开发中，它可以是一种传达复杂数据变化的方式，也是一种将 UI 与业务逻辑分离的方式。但是在我们开始使用这种模式之前，让我们先看一些真实世界的例子，以确保我们理解它是什么。

比方说，你发现了一本每月都发行新杂志的杂志。当所有新的问题出现时，你想要跟上它们。所以你订阅了这份杂志，然后每个月当新的一期杂志发行时，它就会出现在你的门口。很好，对吧？最终你可能会对这些内容感到厌倦，或者厌倦了每月付费，所以你会取消订阅。然后，下一次发行时，它不会出现在你的门口。

这是观察者模式的一个真实例子。不管你是否知道，你可能每天都会遇到这种情况。其他例子包括电子邮件简讯、RSS 订阅或订阅朋友的推文。

# 观察者模式

观察者模式有两个组成部分。**主体**和**观察者**。每个主题可以有许多观察者。当观察者订阅主题时，它会在主题发生变化时得到通知。下面的图表给出了该结构的一个快速概览，但我们将在一秒钟内更深入地挖掘。

[![Diagram of how changes affect the subject and the changes are passed down to the subscribed observers](img/0a468d029e47f0f692705e6600989c32.png)](///static/46761db22dd1694ef0b05d0daed5f3b8/6c283/observer-pattern-diagram.png)

## 主语

把主体看作将要被观察的事物。每个主题都需要一个订阅的方法和一个取消订阅的方法。在内部，这只是在数组中添加和删除一个函数。

一旦设置了主题的订阅和取消订阅，任何时候您想要通知订阅者一些事情，您只需迭代每个订阅者，并将他们应该知道的数据传递给他们。

因此，根据主题是什么，您可以为其他人提供一个方法来更新某个本地状态，并且每次本地状态被更新时，您通过将更新后的状态传递给所有订阅者来让他们知道。

或者主题定期从 am API 中获取新数据，任何时候数据发生变化，它都会通知订阅者。

只要你包括订阅和退订的方法，并在发生重要事情时给每个订户打电话，可能性是无穷无尽的。

这里有一个 observer 主题的超级基本实现的例子。

```
class Subject {
  subscribers = []

  subscribe = (functionToSubscribe) => {
    this.subscribers.push(functionToSubscribe)
  }

  unsubscribe = (functionToUnsubscribe) => {
    this.subscribers = this.subscribers
      .filter(func => func !== functionToUnsubscribe)
  }

  action = (data) => {
    this.subscribers
      .forEach(subscriber => subscriber(data))
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 观察者

一旦主题设置完毕并准备好接受订阅者，您只需向 subscribe 方法传递一个函数就可以开始了。您传递给 subscribe 的这个函数称为观察器。你可以让很多观察者观察一个主题。观察员可以随时添加和删除。观察者应该期待某种与主题相关的数据被传入，并应该因此执行某种操作。

```
// Create a new subject to observe.
const subject = new Subject()

// Create a new function and subscribe to the subject.
const observerFunction = (data) => console.log(data)
subject.subscribe(observerFunction)

// Stuff happens with subject
subject.action('foo')
subject.action('bar')

subject.unsubscribe(observerFunction)

// More stuff happens, but the `observerFunction` will never find out.
subject.action('baz') 
```

Enter fullscreen mode Exit fullscreen mode

# 例子

现在我们知道了观察者模式的所有组件，让我们看几个例子。

## 计数器

在这个例子中，主语是一个计数器。它提供了订阅、取消订阅和添加计数的方法。任何时候你点击+1 或-1 按钮，计数都会改变。

然后有三个观察者存储并显示他们被告知的最后一次计数。最初没有人观察计数，但是您可以通过按下 subscribe/unsubscribe 按钮来切换。

用它玩一会儿，改变订阅和改变计数，看看哪些更新，哪些不更新，什么时候更新。

[https://codesandbox.io/embed/o48m8lm64y](https://codesandbox.io/embed/o48m8lm64y)

## 祝酒

虽然计数器玩起来很有趣，并能很好地显示观察者模式是如何工作的，但它并不太实用。另一方面，祝酒词更实际一些，是这种模式有用的更真实的例子。

如果你以前从未听说过“吐司”这个词，当你在一个网站上时，它是一个短暂弹出的通知(你知道，就像烤面包机里的吐司)。通常它用于在您之前执行的一些操作的结果返回后提供反馈。

所以在我们的例子中，我们的主题是祝酒词，我们的观察者是 UI，它将显示收到的新祝酒词。因此，toasts 主题有订阅和取消订阅新的祝酒词的方法，也有创建成功祝酒词、警告祝酒词和错误祝酒词的方法。

每次调用 success、warning 和 error 方法时，都会向订阅者发送新的祝酒词。当我们的观察者收到新的祝酒词时，它会简单地呈现它。

通过以这种方式实现我们的祝酒词，我们可以有一个主题和一个观察者，并从代码库中的任何地方调用 success、warning 和 error 方法来显示祝酒词。这对于提供一个通用工具非常有用，我们的代码库的其余部分可以使用它来呈现一个 toast，同时将 UI 逻辑保持在需要触发它的业务逻辑之外。

[https://codesandbox.io/embed/1q73rv34q](https://codesandbox.io/embed/1q73rv34q)

# 就是这样！

很简单，对吧？现在你是观察者模式的专家了。这篇文章是正在进行的关于 web 开发设计模式的系列文章的一部分，所以现在去看看其他的或者稍后回来看更多。