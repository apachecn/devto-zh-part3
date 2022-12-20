# 进入 JavaScript &克服挫折

> 原文：<https://dev.to/theallenc/entering-javascript-overcoming-frustrations-48kh>

[![header](img/4e9f23da3e985cb2ddc4f64ad659e1ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3m3Dw9wT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/urs4lo5nsjdz57bc9jya.png)

# 输入 JavaScript &克服挫折

我想以此作为这篇文章的序言，我对这篇博客主题的体验是片面的，因此我要提出的想法可能是有偏见的或直接不正确的。作为一名计算机科学专业的学生，匆忙完成长达七年的学士学位之旅也极大地限制了我做研究的时间。也就是说，这些是我的想法，如果它们是错误的，它们应该被纠正，以便我可以学习。请记住，最终，我们作为学生、工程师，都要花时间在堆栈溢出的深处爬行，寻找提示，而不考虑底层的细节。我们开始吧！

## JavaScript。初学者进入令人困惑的异步编程世界。

JavaScript。光是名字就让人摸不着头脑；许多年轻的开发人员可能不知道它与 Java 无关。如果你仔细研究，你会发现现在 ECMA 标准化语言的早期分类，但我喜欢这样想:

> 网景公司(Netscape)的一些兄弟敢于在大约一周内创造一种脚本语言。他们不知道的是，他们的语言将填补 web 开发空间中正在形成的空白，但有一个警告，他们的侏儒是垃圾。它是垃圾，没人想用它。所以他们通过把“java”变成它的名字来迷惑人们，使他们认为它与所有强大的 Java 有关系。人们会想，“Java 很热，也许它的表亲也很酷。”然后使用它。

这很有效，JavaScript 几乎无处不在！

撇开笑话和恶名不谈，JavaScript(和其他 ECMA 衍生工具)是在高级 web 技术需要解决方案的时候出现的。不幸的是，当 JavaScript 出现时，我正全神贯注地学习走路，但我认为可以肯定地说，虽然它是一个解决方案，但它不是一个理想的解决方案。

正如我在本文开头的 PSA 中提到的，我对 JavaScript 的经验是有限的。几年前，我开始自学 JavaScript，当时我在一家本地软件公司实习，担任测试工程师。当时，我的工作是使用流行的 node.js 测试框架 Mocha 开发和维护一个预备性的自动化测试系统。我公司的主要产品是基于节点的 web 应用程序，所以使用基于节点的测试框架似乎是很自然的。

在实习之前，我的语言经历包括:BASIC、Visual Basic、C/++、Java。当我申请这份工作时，我并不知道 JavaScript 是我将要使用的主要语言，我也不知道是怎么回事，但它从未在面试过程中出现过。我记得我的第一天，我的测试负责人给我布置了一个任务，看关于“回拨地狱和他们基于承诺的解决方案”的视频，但我一点也不理解。然而，随着时间的推移，随着大量的坚持、练习和失败，我学会了真正讨厌 JavaScript。

作用域、语法、异步编程？当这三个东西都是垃圾的时候，这种语言怎么会是 GitHub 上排名第一的语言。当然，我的观点是我缺乏经验的表现，但是直到我开始实习的那个夏天，我才真正学会欣赏 JavaScript 的混乱。

> 说真的。JavaScript 中有三种声明函数的方法，但是每种方法都有自己的特点，这一点很明显。

```
function doThingMakesSense () {
// The classical C-Style function declaration
}

const doThingWhatIsThis = () => {
// This is an anonymous function that's doing a bad job at being anonymous
}

const doThingOkayThisIsRedundant = function () {
// At this point it seems redundant
} 
```

Enter fullscreen mode Exit fullscreen mode

在那个夏天，我有一些空闲时间去做一些自己喜欢的项目。我想为一个不和谐的服务器做一个机器人，我和我的朋友们用它来分享我们所有的潮湿的迷因。有一些不同的框架可以用来连接到 Discord 的 API，这些都是用不同的语言编写的(Java，C#，Rust 等)..).最初我使用了一个基于 Java 的框架，在花了整整一个周末的时间尝试启动和运行 Mavin 配置之后，我决定转向我的新“朋友”JavaScript。

我选定了一个叫做 [Discord.js](https://discord.js.org/#/) 的令人惊叹的 API(说真的，即使这个 API 读起来也是一种享受)。让我惊讶的是，与 NPM 一起启动并运行一个项目是如此之快。安装节点模块只花了我几分钟时间，而不是几小时或几天。在几个小时之内，我已经创建了一个机器人，它不和谐地响应我的消息。不要乱搞 IntelliJ 项目。不要试图集成 Mavin 来确保我的 Java 库是最新的。只是一个简单的，我的项目已经启动并运行。

[![Discord.js Logo](img/b2d17800c51e67673c5b227638b2e574.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zA5DIYAk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.nakamap.com/img/grp/f41b32b612e2bee7b890e92e3912bc841f8356bb_raw.jpg)

整个经历揭示了 JavaScript 近年来变得如此普遍的主要原因；它很灵活。看看我如何快速地使用我非常熟悉的语言启动并运行一个项目，只需找到一个特定的节点模块。

这一切很大程度上可以归功于 Node 令人惊叹的贡献者，但如果没有这个基础，试图在正常环境之外使用 JavaScript 将会像试图使用 Java 来驱动 web UI 一样。

这学期，也是我的最后一个学期，我终于开始涉足 web 开发的世界。我又一次见证了这种杰出语言的另一种应用。在这个讲座中，我们不允许使用任何框架。一切都必须使用有限的工具集从头开始:PHP、HTML、JavaScript(严格来说是 jQuery)。这是为了让我们，作为初学 web 开发的人，了解这些年来开发的众多框架是如何形成的，以及开发它们的原因。由于 JavaScript 是出于让网页更具“互动性”的需要而产生的，这门课在有限的能力范围内教会了我理解 JavaScript 从何而来。

## 克服挫折

最初，我对 JavaScript 缺乏经验，这让我感到非常消极。我会对问工作中的上司问题感到焦虑，因为通常我所有的问题都有非常简单的解决方案。然而，随着我了解的越来越多，我仍然越来越喜欢这门语言，尽管它有很多缺点。现在唯一不同的是，学习我不了解的东西让我兴奋。

当我在 Dev 上滚动浏览 [#javascript](https://dev.to/t/javascript) 标签，阅读人们使用 javascript 的所有令人兴奋的不同应用程序时，我受到了启发。虽然这种态度可能只是我作为一名学生和开发人员逐渐成熟的产物，但我觉得 JavaScript 似乎是我实现某些东西的催化剂:与其因为缺乏经验而沮丧，你应该兴奋地学习新的东西(除非你正在学习 Scheme。那样的话就放弃吧)。

> ### It's foolish to be depressed because of lack of experience ...

总的来说，由于缺乏经验而产生的挫败感是愚蠢的，你应该对学习新事物感到兴奋。正如我们在学校里所学的，也正如我们每天在工作中所经历的，计算机科学行业每天都在发生变化。20 年前，隐私从来不被认为是一个问题，而今天，人们被拖到国会面前就隐私问题作证。我们身处这个行业，每天都要学习新事物，如果你对此不感兴趣，我不知道还有什么能让你感兴趣。

JavaScript 是我不得不自学的第一批真正的东西之一，反思这一点让我非常兴奋，终于完成了我的学业，并真正开始了我在这个困难但有指导意义的领域的冒险。

## 以下是我在这篇文章中使用的一些资料来源。有些还挺有意思的！

*   [https://dev . to/lambdatesting/fixing-JavaScript-跨浏览器-兼容性-问题-51pl？UTM _ source = additional _ box&UTM _ medium = internal&UTM _ campaign = regular&booster _ org = lambdatesting](https://dev.to/lambdatesting/fixing-javascript-cross-browser-compatibility-issues-51pl?utm_source=additional_box&utm_medium=internal&utm_campaign=regular&booster_org=lambdatesting)
*   [https://dev . to/trekhleb/algorithms-and-data-structures-in-JavaScript-49 i3](https://dev.to/trekhleb/algorithms-and-data-structures-in-javascript-49i3)
*   [https://dev . to/tylermcginnis/a-初学者-JavaScript 指南-原型-5kk](https://dev.to/tylermcginnis/a-beginners-guide-to-javascripts-prototype-5kk)
*   [https://dev.to/winduptoy/a-javascript-free-frontend-2d3e](https://dev.to/winduptoy/a-javascript-free-frontend-2d3e)
*   [https://www . site point . com/anatomy-of-a-modern-JavaScript-application/](https://www.sitepoint.com/anatomy-of-a-modern-javascript-application/)
*   [https://www . sitepoint . com/object-oriented-JavaScript-deep-dive-es6-classes/](https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/)