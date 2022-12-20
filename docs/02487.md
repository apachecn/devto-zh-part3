# 关注点分离

> 原文：<https://dev.to/pulljosh/why-is-jsx-okay-1l2g>

# 老办法:按语言拆分代码

在组件流行之前，我们经常将代码分成单独的 HTML、CSS 和 JS 文件。这种划分将相关代码的缠结分离成单独的块，它们之间只有必要的字符串。

按语言划分代码比把所有的东西都放在一个文件里要好，因为它降低了我们滚轮上的压力，并且让我们在错误地假设我们的代码组织得很好的情况下晚上睡觉。

但是连接弦就在那里，它们困扰着我们。

所有这三种语言必然是交织在一起的(如果你改变 HTML 中的一个类名，你也必须更新你的 CSS 和 JS)，所以我们不断地在相关文件之间来回跳转。

# 新方式:按组件拆分代码

一个更好的系统，包括更少的跳转，是根据一起发生的事情来拆分代码。组件帮助我们做到这一点！组件是一种认识，即基于编程语言将我们的代码分割成文件是错误的方法。

一开始是有争议的。有人大声疾呼。公众清楚地表明，这种错综复杂的担忧是对文明社会基础的侵犯。

<figure>

[![Dan the Witch](img/1150d289631e8a05368a63815244a8bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BbzS972h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0d1r6louz13ppynjeltw.png)

<figcaption>For a while we were convinced that [this generous bloke](https://dev.to/dan_abramov) was actually a witch!</figcaption>

</figure>

但是，碰巧的是，将 HTML、CSS 和 JS 结合在一起实际上是一个非常好的想法。关键条件？相反，你必须根据 HTML、CSS 和 JS 的哪些部分协同工作以形成一个连贯的整体来划分你的代码。当我们在组件级别将代码分割成文件时，就会发生这种情况。

[https://codepen.io/PullJosh/embed/wbqyNP?height=600&default-tab=result&embed-version=2](https://codepen.io/PullJosh/embed/wbqyNP?height=600&default-tab=result&embed-version=2)

主要的好处是，我们不再需要在每个文件之间附加条件。(如果我们改变一个类名，所有的事情都发生在一个地方。)在理想世界中，每个组件都是完全独立的，不依赖于其他组件的实现细节来正常工作。这意味着我们不再需要像以前那样频繁地在文件间跳转。

# 变化是唯一的`const`

改变根深蒂固的理念(例如，关注点分离)是非常困难的。幸运的是，web 开发人员似乎对此相当擅长。这个行业发展很快，有时我们似乎过于频繁地重新发明这种做法。但是回顾过去的想法提醒我们，这些改变是值得付出痛苦的。继续努力吧，伙计们！✌