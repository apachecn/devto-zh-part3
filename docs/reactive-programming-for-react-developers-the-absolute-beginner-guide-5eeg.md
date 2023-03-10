# React 开发人员的反应式编程——绝对初学者指南

> 原文：<https://dev.to/petyosi/reactive-programming-for-react-developers-the-absolute-beginner-guide-5eeg>

## [T1】简介](#intro)

在我最近的冒险中，我决定找到一种真正的方法来管理复杂的 React 组件的内部状态。虚拟化列表是一件复杂的事情。用户滚动和加载新数据，项目因图像加载而调整大小，设备改变方向。有时，上述所有情况同时发生。

我是从一家类似 redux 的店开始的。几天后，我陷入了纠结的嵌套归约和一堆我无法摆脱的重复。可用的测试无法帮助我找出为什么组件会以意想不到的方式运行。

我很快就到了:

[![](img/b70b2029748c424708f1b61b2fd2c805.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FL9qTOG5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AwT14bv4NEbNAvDLfTAy9ng.jpeg)

###### 艺人:[马努短号](http://ma.nu/graphics/)

一定有更好的办法。我想大概是这样。

## 我们试试反应式编程吧！输入 RxJS

在学习反应式编程的同时搞清楚 RxJS 是很难的。RxJS 极其强大，但代价是复杂。一堆抽象概念，它们不能立即点击或者以与我理解的完全相反的方式命名——热的和冷的可观察对象、观察者、订户、订阅和调度程序，天啊。尽管如此，我还是设法熬过来了。多亏了 [Learn RxJS](https://www.learnrxjs.io/) 和 [RxMarbles](https://rxmarbles.com/) ，组件库重新做好了。(不要钻研上面的资源，记住它们以后用。它们又长又棒，而且很快就硬了。先把这篇文章写完)。

结果实现很漂亮，是 redux 存储大小的 1/4，可测试且可靠。当然，情人眼里出西施。RxJS 商店实现附带了 Virtuoso 的第一个版本。

他们说，看起来棒极了。他们说，我不会使用它，RxJS 太大了，我不可能把它作为一个依赖项添加进来。叹气。

邓宁-克鲁格效应开始起作用。我对反应式编程有足够的了解，可以用一个小型的自己开发的实现来代替 RxJS。在我添加更复杂的特性之前，最好早点这样做。

它起作用了，带来了通常数量的难以追踪的错误。

他们说，你在重新发明轮子。检查通话包，他们说。我应该早点问的。无论如何，本土的解决方案是存在的，而且是有效的。更好是足够好的敌人。不要浪费时间，完成你已经开始的事情。让我快速浏览一下[的通话包文件](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)...

> 所以我们别废话了。
> 
> **反应式编程是用异步数据流**编程。
> 在某种程度上，这并不是什么新鲜事。事件总线或您的典型点击事件实际上是一个异步事件流，您可以在其上观察和做一些副作用。反应是类固醇的概念。你可以创建任何东西的数据流，而不仅仅是点击和悬停事件。流廉价且无处不在，任何东西都可以是流:变量、用户输入、属性、缓存、数据结构等等。例如，假设您的 Twitter feed 是一个与点击事件相同的数据流。你可以听听那个流，然后做出相应的反应。
> 
> 最重要的是，你有一个惊人的工具箱的功能来组合，创建和过滤任何这些流。这就是“功能性”魔法发挥作用的地方。一个流可以作为另一个流的输入。甚至多个流可以用作另一个流的输入。你可以合并两个流。您可以过滤一个流，以获得另一个只包含您感兴趣的事件的流。您可以将数据值从一个流映射到另一个新的流。

我们走吧。这是我认为每个人都需要的起点。这是我写这篇文章的灵感。

接下来是我认为你需要了解的关于反应式编程的最基本的东西**，**作为玩笑测试**。我将坚持在 React 项目中有用的东西，直接绑定到 DOM 元素事件没有多大意义。这是[完整的测试套件](https://codesandbox.io/s/modest-gauss-huyw7)——让我们完成每一项测试。我们使用 Callbag 作为底层实现。然而，对于 RxJS 来说，上面所有的看起来都是一样的。如果你不懂 Jest，也不用担心。断言应该是不言自明的。**

## 主题，订阅者和观察者

```
test("subject emits values to its subscribers", () => {
  const a = subject();

  const subscriber = val => expect(val).toEqual("foo");
  observe(subscriber)(a);
  // Ignore the "1" parameter for now
  a(1, "foo"); 
}); 
```

这几乎抓住了反应式编程的全部思想。在上面，我们:

*   创建一个流`a`，在我们的例子中它是一个通用主题；
*   创建一个订阅者，它作用于来自流的值(在这种情况下，我们验证我们收到了正确的值)；
*   我们使用`observe`将订户连接到流；
*   我们在流`a`中推送`"foo"`；

如果以上对你有意义，恭喜你！其余的例子只是在此基础上添加了一些小块。

## 行为主体

```
test("behavior subject emits previously pushed values to new subscribers", done => {
  const a = behaviorSubject("foo");

  a(1, "bar");

  const subscriber = val => {
    expect(val).toEqual("bar");
    done();
  }

  observe(subscriber)(a);
}); 
```

接下来，我们有行为主体。它的行为与普通主题非常相似，只有一个例外——行为主题**是有状态的**。这意味着附属的订户将立即被调用主题的最后一个值。此外，它是用初始值构造的。简而言之，订阅这样的主题意味着你会被立即调用。

行为主题在 Virtuoso store 实现中很普遍——这是它保持大部分状态的地方。

## 管道和地图

```
test("pipe and map transform the incoming stream values", done => {
  const a = subject();

  const subscription = val => {
    expect(val).toEqual(2);
    done();
  };

  const b = pipe(
    a,
    map(i => i * 2)
  );

  observe(subscription)(b);

  a(1, 1);
}); 
```

传递值没什么意思。感谢`pipe`和`map`，我们创建了一个**输出流** ( `b`)，它转换并发出来自`a`的值。

## 结合起来

```
test("combine takes values from two streams", done => {
  const a = subject();
  const b = subject();

  const subscription = vals => {
    expect(vals).toEqual([1, 2]);
    done();
  }
  const c = pipe(combine(a, b))

  observe(subscription)(c)

  a(1, 1);
  b(1, 2);
});

test("pipe, combine and map work together", done => {
  const a = subject();
  const b = subject();

  const subscription = val => {
    expect(val).toEqual(3);
    done();
  }
  const c = pipe(
    combine(a, b), 
  map(([aVal, bVal]) => aVal + bVal )
  )

  observe(subscription)(c)

  a(1, 1);
  b(1, 2);
}); 
```

我认为这是我们最不需要的工具。它允许您构建一个输出流，该输出流转换来自两个或更多输入流的输入值。

## 奖金- `scan`和`sampleCombine`

[测试套件](https://codesandbox.io/s/modest-gauss-huyw7)包括另外两个测试，展示了`scan`和`sampleCombine`如何工作。如果到目前为止这些例子对你有意义，你应该不难理解它们。

请继续关注下一篇文章，我们将从主题构建一个简单的商店，并将其与 React 组件集成。我们还将讨论当你可以使用钩子、Redux 等工具时，为什么还需要这样做。

与此同时，从[和](https://twitter.com/andrestaltz)的中重温一下你一直错过的[反应式编程入门。您还可以派生出沙箱，并为 Callbag Wiki](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754) 中列出的一些 [callbag 实用程序添加更多测试。在评论里分享你的叉子吧！](https://github.com/callbag/callbag/wiki)