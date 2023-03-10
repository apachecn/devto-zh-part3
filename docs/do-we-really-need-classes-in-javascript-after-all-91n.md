# 我们真的需要 JavaScript 中的类吗？

> 原文：<https://dev.to/smalluban/do-we-really-need-classes-in-javascript-after-all-91n>

在许多其他出色的功能中，ES2015 引入了类语法。对某些人来说，它是面向对象编程中缺少的一部分；对其他人来说，有些东西我们从一开始就不应该加进去。然而，类已经受到库作者和用户的喜爱，所以今天，你可以在几乎每个 JavaScript 库或框架中找到它们。

课程兑现了他们的承诺吗？三年后，我可以说除了语法更简单(而不是使用函数构造函数和原型)之外，他们在各个领域都失败了。让我们一起探索一些最容易犯的错误。

```
class MyComponent extends CoolComponent {
  constructor(one, two) {
    // Use super() always before calling `this`
    // and don't forget to pass arguments 🤭
    super(one, two);
    this.foo = 'bar';
  }

  update(...args) {
    this.value = '...';
    // Does CoolComponent include update method or not? 🧐
    super.update(...args);
  }
} 
```

**类语法可能会令人困惑。**库通常强迫用户使用`extends`关键字来消费它的 API。看起来很简单，扩展需要在任何需要的地方使用`super()`调用。为了确保我们的方法不会覆盖父方法定义的内部方法，我们必须小心如何命名它们(很快就有可能使用一个奇特的`#`关键字来创建[私有字段](https://github.com/tc39/proposal-class-fields#private-fields))。

超级调用也很棘手——例如，在调用`super()`之前，不能在构造函数中使用`this`。哦，不要忘记传递构造函数参数。如果你定义了`constructor`方法，你必须手动完成。

当然，我们可以习惯。我们照做了。但是，并不代表这就是对的。

```
class MyComponent extends CoolComponent {
  constructor() {
    ...
    // Change onClick method name and forget update it here 😆
    this.onClick = this.onClick.bind(this); 
  }

  onClick() {
    this.foo = 'bar';
  }

  render() {
    return <button onClick={this.onClick}>...</button>;
  }
} 
```

**类与这个语法**紧密绑定。在类方法中`this`代表类的一个实例。从来没有打算将方法定义传递给另一个实例并丢失该上下文。我知道库的作者只是想从类语法中挤出一些可能的东西，同时保持创造性。不幸的是，没有一个绑定函数上下文的最佳解决方案。为了挽救，我们将能够使用另一种新的语法- [类字段](https://github.com/tc39/proposal-class-fields)，这简化了创建预绑定到实例的方法。

```
class MyComponent extends CoolComponent {
  // this method uses current state 🤨
  foo() {
    this.currentValue += 1;
    return this.currentValue;
  }

  // this method depends on other instance method 👆
  bar(nextValue) {
    const value = this.foo();
    return value + nextValue;
  }
}

class OtherComponent extends MyComponent {
  // Ups, this.bar() is broken now 😡
  foo() {
    return this.otherValue; 
  }
} 
```

**课难作文**。这里的第一个问题是有状态方法。它们可以使用当前状态并返回不同的结果，即使是相同的输入(传递的参数)。第二个因素是众所周知的大猩猩-香蕉问题。如果你想重用类定义，你必须要么全部接受，要么什么都不接受。即使您知道 parent 包含哪种方法，它们将来也可能会改变，所以破坏某些东西是相当容易的。

此外，从类定义中取出一个方法，并在另一个中重用它几乎是不可能的。方法通常相互依赖，或者使用这种语法从类实例属性中获取值。是的，有一个 mixins 模式，但是它没有提供一个简洁明了的方法来构造类。如果你想知道，有一个 mix with T1 项目，甚至还有来自同一作者的 T2 ES 提议 T3。

那些障碍有什么出路吗？尽管有这些课程的负担，但它们无疑是 web 开发的最佳途径。我们以前使用普通对象的方式并没有提供比类更大的优势。正因为如此，图书馆的作者和用户不假思索地转向了他们。那么，有没有可能避免所有的类问题，并创建一个 UI 库，同时仍然强大且易于使用？

在过去的两年里，我一直在开发一个用于创建 Web 组件的库，我称之为[混合体](https://github.com/hybridsjs/hybrids)。顾名思义，它混合了两种思想——类和普通对象。然而，最终的解决方案并没有就这样来到我的面前。

最初，我像其他库一样遵循通用模式。我在这些类的基础上构建了我的 API。虽然，这个库的主要目标是将业务逻辑从定制元素定义中分离出来，让用户避免一些类问题(例如`extends`和`super()`)。过了一年，我的工作差不多完成了，我准备发布一个主要版本。唯一让我很困扰的最后一件事就是缺乏作曲机制。与此同时，我开始了解更多关于函数式编程的知识，我非常喜欢它。那时我确信类语法是一个障碍。我试着研究了很多关于如何组成类，但所有的解决方案在我看来都是不充分的。

只有当你放弃现有的解决方案，转而创造新的解决方案，突破才会发生。对我来说，这是我们如何定义组件的思想转变。所有这些问题都成为再次启动这一进程的动力，但这一次是以完全不同的方式。我没有使用现有的想法，而是从一个空文件开始，尝试创建一个公共 API 示例，解决了这些问题。最后，我以类似这样的话结尾:

```
import { html, define } from 'hybrids';

function increaseCount(host) {
  host.count += 1;
}

const SimpleCounter = {
  count: 0,
  render: ({ count }) => html`
    <button onclick="${increaseCount}">
      Count: ${count} </button>
  `,
};

define('simple-counter', SimpleCounter); 
```

在普通对象的定义中，既没有类，也没有语法，只有简单的值和纯函数。此外，对象定义可以很容易地组合，因为它们是独立属性的映射。自定义`define()`函数动态创建一个类，在原型上应用属性定义，最后使用自定义元素 API 定义一个自定义元素。

起初，我认为以这种方式实现 API 是不可能的，它可以扩展并允许构建比简单的计数按钮具有更多逻辑的复杂组件。然而，日复一日，我试图创造更好的想法和解决方案，使这成为可能。

努力工作得到了回报。2018 年 5 月，我发布了该库的主要版本。你可以在上面看到的代码是来自文档的一个[完全工作的例子](https://stackblitz.com/edit/hybrids-simple-counter?file=simple-counter.js)！所有这一切之所以成为可能，是因为大量的思想结合在一起使用，比如属性描述符、工厂、属性转换，以及带有变更检测的缓存机制。

**然而，从题目来看，开篇的问题呢？我的想法就是答案吗？时间会证明一切。现在，我很乐意和你讨论这个话题💡。**

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[hybrids js](https://github.com/hybridsjs)/[hybrids](https://github.com/hybridsjs/hybrids)

### 从普通对象和纯函数创建 web 组件的最简单方法！💯

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![hybrids - the web components](img/7835334dab31a0355c19ba1881e91e6e.png)](https://raw.githubusercontent.com/hybridsjs/hybrids/master/docs/assets/hybrids-full-logo.svg?sanitize=true)T3】

[![npm version](img/f51c6faa48d12fc66c192611c2bce14c.png)](https://www.npmjs.com/package/hybrids)[![bundle size](img/6d77b82b441c48627c3606727cc6f651.png)](https://bundlephobia.com/result?p=hybrids)[![types](img/ae8d21e43a3e99a5dacf7ca277aea4b4.png)](https://github.com/hybridsjs/hybrids/blob/master/types/index.d.ts)[![build status](img/8689c4b9bda3f6ed5d619260f06548c6.png)](https://travis-ci.com/hybridsjs/hybrids)[![coverage status](img/0a6adeb5f4a7c3b3a794bc56b6b3a8f5.png)](https://coveralls.io/github/hybridsjs/hybrids?branch=master)[![npm](img/677fd697a9cf3c069bbe8c7edf990baa.png)](https://www.npmjs.com/package/hybrids)[![gitter](img/01762118d9c9f4f27c97f515b891c459.png)](https://gitter.im/hybridsjs/hybrids)[![twitter](img/851bb2f2a7ba78ec30708acc8199736c.png)](https://twitter.com/hybridsjs)[![Conventional Commits](img/8f23e0548705c4a7a6f3953619fbf266.png)](https://conventionalcommits.org)[![code style: prettier](img/2113a413b334e649fd64b7626cd40572.png)](https://github.com/prettier/prettier)[![GitHub](img/4eaddbe61720705112ebf68348398c77.png)](https://raw.githubusercontent.com/hybridsjs/hybrids/master/LICENSE)

> <g-emoji class="g-emoji" alias="medal_sports" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3c5.png">🏅</g-emoji>2019 年[开源奖](https://osawards.com/javascript/)的**“年度突破”**类四个提名项目之一

**Hybrids** 是一个 UI 库，用于创建 [web 组件](https://www.webcomponents.org/)，具有基于简单对象和纯功能的强大声明性和功能性方法。

*   **最简单的定义**——只是简单的对象和纯粹的函数——没有`class`和`this`语法
*   **无全球生命周期** —独立的资产，拥有自己简化的生命周期方法
*   **继承之上的组合** —易于重用、合并或拆分属性定义
*   **超快速重新计算** —内置智能缓存和变更检测机制
*   **没有外部工具的模板** —基于标记模板文字的模板引擎
*   **开发人员工具包括**——热模块替换支持，实现快速愉快的开发

## 入门指南

将 hybrids [npm 包](https://www.npmjs.com/package/hybrids)添加到您的应用中，或者使用 unpkg.com/hybrids[CDN 在浏览器中直接使用。](https://unpkg.com/hybrids@%5E4/src)

然后，导入所需的特征并定义您的定制元素:

```
import { html,
```

…</article>

[View on GitHub](https://github.com/hybridsjs/hybrids)

* * *

你想知道更多吗？在我即将发布的帖子中，我将详细解释混合库的所有核心概念。现在，我鼓励你看看[项目主页](https://github.com/hybridsjs/hybrids)和[官方文档](https://hybrids.js.org)。

你也可以观看我在 2018 年 10 月的 [ConFrontJS](http://confrontjs.com) 会议上发表的[用功能性 Web 组件](https://youtu.be/WZ1MEHuxHGg)品味未来的演讲，在那里我解释了我是如何产生这些想法的。

* * *

🙏你如何支持这个项目？给 GitHub 库一个⭐️，在⬇️下面评论，把混血儿的消息传播到全世界📢！

* * *

👋欢迎开发者加入社区！我的名字是 Dominik，这是我写的第一篇博客——欢迎任何反馈，❤️.

<small>封面照片由[扎克·卢塞罗](https://unsplash.com/photos/qAriosuB-lY)在[Unsplash](https://unsplash.com)T5 拍摄</small>