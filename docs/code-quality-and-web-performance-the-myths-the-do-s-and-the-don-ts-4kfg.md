# 代码质量和网络性能，神话，该做的和不该做的

> 原文：<https://dev.to/duranenmanuel/code-quality-and-web-performance-the-myths-the-do-s-and-the-don-ts-4kfg>

*本文原载于[Enmascript.com](https://enmascript.com/articles/2019/06/04/code-quality-and-web-performance-the-myths-the-dos-and-the-donts)T3】*

优化是一个包含大量可能性的词，尤其是在软件开发领域。开发人员总是喜欢“优化”，但是什么是真正的“优化”？-这个词经常被忽略-用来为主观观点开脱，其他时候它只是用来证明并非真正必要的“改进”。在本文中，我们将讨论作为开发人员，在试图通过“优化”来改进代码库时所犯的错误，并且，我们将了解哪些是开始正确地做这件事的好方法。

## 永远先衡量，量化，然后请...不要一味的“优化”。

作为软件开发人员，我们多年来学到的一个基本经验是**数字很重要**，*如果你有办法量化你为之付出的努力和它给出的结果之间的相互关系，那么某事就是有影响力的*。

例如，人们普遍认为代码行数与更高的性能或更好的质量成反比。

[![](img/90afcd1d7ef8f816f680d810ba394f65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tcD6WDPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://enmascript.cimg/2019-06-03-common-belief.jpeg)

通常情况下，代码越少意味着要覆盖的表面区域越少，这意味着更加清晰，出现错误的可能性越小。然而，这仅适用于如果你有一个“高质量”的代码库，它是安全的，一致的，并且使用正确的工具完成正确的工作。

让我稍微解释一下，以下面这段代码为例:

```
const mainWrapper = document.querySelector('.wrapper');

if (mainWrapper) {
    const element = document.querySelector('.inner-box');

    element.classList.add(enabledInteractionClass);
    mainWrapper.classList.remove(enabledInteractionClass);
} 
```

上面的代码没有问题。它只是多了几个变量，使代码更具声明性。我见过代码审查，人们将这个实现转换成:

```
const mainWrapper = document.querySelector('.wrapper');
let enabledInteractionClass = 'enabled-box';

const enabledElementClass = mainWrapper
    ? document.querySelector('.inner-elm').classList.add(enabledInteractionClass)
    : mainWrapper.classList.add(enabledInteractionClass); 
```

然后，当一个开发人员说，“我们可以避免额外的变量给`document.querySelector('.inner-elm')`”并且他/她称之为优化时，你的回答是你更喜欢第一种方式，因为相比之下它更具声明性，并且你花了 5 分钟讨论这个问题。代码评审者没有意识到的是，他现在一直在定义`enabledInteractionClass`，他在用变量`enabledElementClass`来表示不需要它的东西，那么改进在哪里呢？这是一个基于坚实基础的决定吗？这段代码如何让代码库变得更好？它是可测量的吗？

**免责声明**:我赞成构造良好/使用良好的三元运算符和[短路](https://enmascript.com/articles/2018/07/09/short-circuiting-for-faster-evaluations)，这个例子只是说明*而不是*如何仅仅为了减少代码行或捍卫主观观点而使用工具。

## 有一个术语是用来形容那些以浪费时间而告终的毫无价值的微优化。

<mark>*骑自行车是一种浪费时间的艺术，在次要的、边缘的问题上进行技术争论，这些问题不会对你的工作产生真正积极的影响，而有更重要的事情要做。*</mark>*所以，下次有人想争论无关紧要的改进时，就说*这个词*。这个术语的词源可以在[wiktionary.org](https://en.wiktionary.org/wiki/bikeshedding#English)和[城市字典中找到另一个很好的定义](https://www.urbandictionary.com/define.php?term=bikeshedding)。*

 *关键是，如果你想优化一些代码，首先要测量和识别瓶颈，微优化是你应该避免的。

## 先测量

假设你的网站/应用程序在滚动时有点慢，不要只是去增加听众的`throttle`时间，并将其标记为*完成*，分析正在发生的事情，并理解这个问题背后的原因。测量滚动性能的一种方法是通过*每秒帧数(FPS)* ，所以你知道你有一个可量化的方式来了解正在发生的事情和一个起点。

### 让我们来看看解决这个问题的好方法

1.  首先，检查在卷轴上执行的代码/任务是否在做它们应该做的事情，卷轴无用可能是由内存泄漏引起的。
2.  如果代码是错误的，修复它，如果它是好的，看看你的替代品:
    *   我们可以用`IntersectionObserver`代替滚动事件吗？
    *   需要`throttle`吗？我们能不能`debounce`一下？
3.  如果看了你的选择后，你没有看到任何其他选择，那么作为最后的资源*考虑*增加`throttle`时间。

为什么要经历这么多麻烦而不是增加时间呢？因为没有什么能保证你在增加时间后不会再发生，甚至更糟，没有什么能保证下面没有更重要的问题。

这只是一个应用于滚动性能的示例，但是通过使用正确的度量标准，同样的原理可以应用于任何其他情况。

## 提高你代码库的质量

优化代码库的一些最常见的方法是 linters，如果你使用 javascript [ESLint](https://eslint.org/) 是一个很好的方法——至于你的风格，你可以使用 [Stylelint](https://stylelint.io/) 。您可以在以后添加这两个元素，作为构建过程中的必需步骤，以创建一个额外的一致性层，linters 是促进代码库一致性的一个好方法。

然而，仅仅依靠 linters 并不能保证你的代码库的质量，它很大程度上取决于你的团队的水平，你必须意识到这一点。开始提高代码库质量的一些建议是:

1.  **推广设计模式**。(我们已经写了其中的一些，即[观察者模式](https://enmascript.com/articles/2019/03/09/diving-into-the-great-observer-pattern-in-javascript)、[工厂模式](https://enmascript.com/articles/2018/10/05/javascript-factory-pattern)、[构建器](https://enmascript.com/articles/2019/03/18/building-objects-progressively-with-the-builder-pattern-in-javascript))你不需要明确地使用它们，记住，这个想法是为正确的工作使用正确的工具，设计模式并不总是最好的选择，但是它们肯定是一种为你的应用程序增加一致性和安全性的方法。

2.  **配对会议/头脑风暴。**沟通和团队合作是一些被严重低估但却非常有效的工具，确保团队在实施想法之前至少与另一个人进行沟通，这样可以确保知识传播，更多人参与进来并发表意见。你们将一起找到解决问题的更可靠的方法，并且你将避免被拒绝的拉动请求的疲劳，促进团队沟通的几个好方法是 [Katas](https://en.wikipedia.org/wiki/Kata_(programming)) 和 [RFC 的](https://en.wikipedia.org/wiki/Request_for_Comments)。

3.  你未来的自我很重要。当你写代码或进行代码评审时，想想你未来的自己，如果你必须扩展你的模块，明天会发生什么？我喜欢遵循 *DIRTFT(第一次就做对)*的原则，这当然不容易做到，即使在今天，我第一次做的时候也没能写出尽可能好的代码，但是我会不断迭代，直到我觉得可以胜任为止。

4.  编码前先思考。创建一个关于你必须做什么的模型，理解什么是需要的，你有什么选择，在分析实现之前不要马上开始写代码。

5.  **关心数据结构和时间/空间复杂度**。这一点需要对**如何**算法工作和**为什么**有更深入的了解。我个人注意到，团队越有经验，他们就越关心这个，在前端世界有一个错误的概念，即数据结构/时间空间复杂性并不重要，但这是一个错误，这些工具允许你编写可伸缩/高性能的模块，这是为正确的工作选择正确工具的一部分。

代码质量没有一个固定的公式，这是一个渐进的主观的话题，它高度依赖于你工作的公司和你的团队的需求。我能给你的最后一个建议是**考虑推广声明性代码**，注意可读性。

## 优化您网站(性能)的建议、提示和良好实践:

测量站点性能的一个很好的方法是通过 [RAIL 模型](https://developers.google.com/web/fundamentals/performance/rail)。下面我列出了网站面临的一些常见问题和一些可能帮助你解决这些问题的工具，这个列表并不是要直接解决你的问题，而是给你指明正确的方向:

#### 未使用的代码

*   **影响指标**:字节/下载时间
*   **有用的工具**:[web pack-bundle-analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer)， [Chrome 覆盖率](https://developers.google.com/web/updates/2017/04/devtools-release-notes#coverage)
*   **可能的解决方案** : [树摇晃](https://webpack.js.org/guides/tree-shaking/)

#### 滚动垃圾

*   **影响指标**:每秒帧数(FPS) /页面交互
*   **有用工具** : [Chrome 的 FPS 计](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/reference#fps-meter)， [Chrome 的 FPS 图](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/reference#fps-chart)， [Chrome 的帧部分](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/reference#frames)
*   **可能的解决方案** : [路口观测器](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)，[节流](https://enmascript.com/articles/2019/02/27/higher-order-functions-and-some-great-applications-in-javascript#throttler)，[去抖](https://lodash.com/docs/4.17.11#debounce)

#### 重/慢动画

*   **影响指标**:每秒帧数(FPS) / UI / UX
*   **有用的工具** : [同卷轴垃圾](#scroll-junk)
*   **可能的解决方案** : [CSS 将改变属性](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change)，[使用由 GPU 处理的属性](https://developers.google.com/web/fundamentals/performance/rendering/stick-to-compositor-only-properties-and-manage-layer-count)

#### 网络请求数量增加

*   **影响指标**:秒/毫秒*加载时间* /应用响应时间
*   **辅助工具** : [记忆装置](https://enmascript.com/articles/2019/04/22/avoid-recomputing-heavy-tasks-by-leveraging-memoization-in-javascript#a-better-implementation)，[再](https://redis.io/)
*   **可能的解决方案** : [记忆化](https://enmascript.com/articles/2019/04/22/avoid-recomputing-heavy-tasks-by-leveraging-memoization-in-javascript)，[缓存](https://en.wikipedia.org/wiki/Cache_(computing))

#### 内存泄露

*   **影响指标**:字节/浏览器内存
*   **有用的工具** : [Chrome 的堆快照](https://developers.google.com/web/tools/chrome-devtools/memory-problems/heap-snapshots)
*   **可能的解决方案** : [闭包](https://www.w3schools.com/js/js_function_closures.asp)，[移除分离的 DOM 节点](https://developers.google.com/web/tools/chrome-devtools/memory-problems/#discover_detached_dom_tree_memory_leaks_with_heap_snapshots)

#### 额外工具

还有许多其他指标和重要的优化你可以做，这是不可能的，适合他们都在一个列表中，但下面的工具是一个很大的帮助，就网页优化而言:

*   [灯塔](https://developers.google.com/web/tools/lighthouse/)
*   [webpagetest.org](https://www.webpagetest.org/)

## 这只是一个起点

通往 web 性能和代码质量的道路是漫长的，在大多数情况下并不简单，有些事情你只能从经验中学习。对于那些正在寻找方向但不知道从哪里开始或如何开始的人来说，这篇文章是一个起点。

希望你喜欢这篇文章，下次再见！*