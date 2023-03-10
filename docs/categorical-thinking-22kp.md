# 分类思维

> 原文：<https://dev.to/stereobooster/categorical-thinking-22kp>

这篇文章的灵感来自于斯坦福大学教授罗伯特·萨波斯基的演讲，其中略述了分类思维的一些观点。我发现理解软件开发的某些方面很有帮助。

## 什么是范畴？

分类思维是我们大脑用来处理大量信息的技巧之一。大脑不是处理给定对象的每一个细微差别，而是分类(桶)，处理更少数量的对象(类别)。作为一名软件开发人员，我可以假设这种“压缩”提高了处理和存储的速度(最好参考关于生物学的科学论文，以获得更权威的意见)。

当我们不得不处理连续的信息时，这个技巧尤其“有用”。例如，有整个光谱的颜色，但我们往往只能分辨彩虹中的 7 种颜色(至少说英语的人是这样；感谢牛顿，他相信炼金术和数字 7 的神秘力量。

类别是一些任意选择的桶。不一定是由某种“逻辑”所支配的。类别的选择可以由文化和语言决定，也可以随时间而改变。回到彩虹的例子，不是所有的语言都有相同的颜色词汇。例如，俄语和希腊语都认为浅蓝色和深蓝色是不同的颜色。专业从事颜色工作的人，比如画家，可以分辨更多的颜色(还记得这个迷因“艺术家 vs 普通人”吗？).重要的是要理解我们有相同的视觉(不考虑任何种类的障碍)，但是我们有一个问题要区分(这需要我们更多的时间，或者我们更容易出错或更难回忆)，这取决于我们的颜色词汇有多丰富。

相关思想:抽象思维、符号思维、刻板印象、模式识别。

## 这怎么可能出错？

当类别的选择是任意的，而不是由某种普遍逻辑(像数学中的普遍逻辑)决定的，那么分类思维就会导致问题。

### 事物归入一类时，即使不同，也可视为相似

[![](img/297f220f1f7c73c7465fd60a58bb2b8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bPdF7lBM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/18gt2z7parlbb9aql8jh.png)

正如我们所见，物体`a`和`b`距离很远，但它们属于同一类别，因此它们可以被视为同类。

在编程中，这个原则表现为[“复制远比错误的抽象便宜”](https://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction)。

例如， [Evan Czaplicki 解释了复选框列表是如何被视为完全不同的](https://www.youtube.com/watch?v=XpDsk374LDE)。

### 当事物归入不同的类别时，即使它们相似，也可以被视为不同

[![](img/af7b7a44e9af333bf84d56c6931a89cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AnWwf7B9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/280lxlxnlva7oxubasqs.png)

正如我们所见，对象`a`和`b`非常接近，但它们属于不同的类别，因此它们可以被视为不同的种类。

比如关于 CSS vs . CSS-in-JS 的无限争论。从这些类别的角度来看，CSS 模块属于 CSS-in-JS，但它更接近于 CSS 的 BEM 方法。

### 分类思维会困住你，你很难看到全局

专注于类别时，我们有时会忘记停下来重新评估我们正在处理的事情。

在编程中，这一原则体现为“工具法则”，例如，如果你唯一的工具是一把锤子，那么你很容易把一切都当成钉子。

例如，关于 OOP 和函数式风格等不同范例的争论。用两种风格编写相同的应用程序是可能的，所以在它的背后有一些共同的东西，但是程序员争论哪种类型更好。

### 对范畴的不同理解

类别是任意选择的。当这些选择没有正式的基础时，不同的人会做出不同的选择，这将是混乱的根源。

在编程中，这表现为粗略的术语，对代码没有精确的含义或“品味”。

例如[强类型 vs 弱类型](https://dev.to/stereobooster/pragmatic-types-dynamically--statically--gradually--weakly--strongly--and-un-typed-languages-5gf4)，有些人会因为内存不安全而认为 C++弱，有些人会因为隐式强制而认为 JS 弱，有些人会因为缺少多态性而认为 Go 弱。实际上，这种粗略的术语毫无用处，必须用更精确的术语来代替。

## 结论

注意你使用的类别，确保你不会被困在一组桶里。