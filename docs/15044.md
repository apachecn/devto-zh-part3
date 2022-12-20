# 我如何识别我的知识差距并学习？

> 原文：<https://dev.to/bgord/how-do-i-identify-my-knowledge-gaps-and-learn-4mlc>

在这篇文章中，我将向你展示我收集主题的工作流程和有效学习它们的方法。

没有人什么都知道，这是肯定的。每天，我都发现自己不知道一些东西，不管是语法、概念还是模式。一些遗漏的答案只需谷歌(或 duckduckgo)搜索一次，所以记住我能在一分钟内找到的任何东西可能是浪费内存和精力。但是概念呢？是的，他们中的大多数是不可能在很短的时间内被“发现”的，并且需要大量的学习努力。

免责声明:这是我的过程的文档——它们对我有用，但不一定适合你。

# 识别知识缺口。

如果我想在某个领域变得更好，我需要知道我不知道的东西。我回答了以下问题，尽量不要过于具体。这仍然是整个过程的早期阶段，我只想看到一个大的画面。

1.  在接下来的 3-6 个月里，什么样的**新**技术/概念肯定会在我的日常工作中获利？

2.  我想在哪个领域加深我的知识？

3.  哪项技术/概念**让**我兴奋，让我想尝试一下？

写下答案后，我给自己几天时间来消化它，并删除一些不必要的/过时的观点。整理完之后，我就进展到下一点了。

# 细化

起初，我的列表是这样的:

```
1\. What new technology/concept would be certainly profitable in my day to day job in the next 3-6 months?

* TypeScript
* Docker
* Observables
* state charts/finite automata

2\. What field do I want to **deepen** my knowledge in?

* maintainable CSS and accessibility
* new React patterns (hooks, Suspense)
- better testing (JS/React)
* Git
* more of an advanced vanilla JS and functional concepts/patterns
* Bash scripting
* Vim/Tmux workflow
* UNIX command line programs (sed, grep, awk, etc.)
* RegExps
* Vue
* Nginx

3\. What piece of technology/concept **excites** me and makes me want to try it?

* GraphQL
* Rust/Go/Elixir
* basic machine learning algorithms
* designing and prototyping with Figma
* D3.js 
```

Enter fullscreen mode Exit fullscreen mode

我做的下一件事是优先排序。我按照主题的重要性对这三个列表进行了排序。

当我开始学习一个新的事物时，我的脑海中会有太多的事情发生。我想找到一种技术来帮助我减少不知所措的感觉，并且能够解决一些小问题，而不是一个大问题。

可能显而易见的是，一开始听起来像“Docker”的观点似乎不可接近，而是一个问题“如何为 Node.js 应用程序设置 Docker 容器以将“Hello world”打印到控制台？”似乎是个不错的起点。

为了跟踪我的旅程，我创建了一个 git 存储库，自述文件成为了我的“2019 年路线图”。我将列表中的每一点提取到一个单独的部分，这样我就可以向每一点添加问题。

这是重要的部分:当我识别出一个问题时，我会将它添加到匹配部分下的列表中，以使它保持最新。

一个基本部分的例子:

```
# TypeScript (0/5)

* How to set up React app with TypeScript?
* How to add types for primitive values like string/number/boolean to a single variable?
* How to type objects?
* How to type functions? What about optional and default parameters?
* What's the difference between type/interface?
* How to type React classes and function components?
* What is a type inference?
* What are generic types and how to use them?

and so on... 
```

Enter fullscreen mode Exit fullscreen mode

我试图忘掉尽可能多的想法。在一个地方看到这些问题是非常新鲜的。我意识到在我回答完这些问题后，我可以流利地打字了。厉害！

我(几乎)每天都在完善我的路线图，它包括重新措辞和分解问题，使它们更加平易近人和具体。

# 学习

当我将这些知识应用到一些现实生活的项目中或者用它创建一些片段时(一种避免“枯燥”学习的方法)，它给了我最大的价值。

为了保留我的问答，我为路线图的每个部分创建了一个单独的降价文件。每次学东西都写到对应的文件里。它让我以后每次都能轻松访问。保持实用。

答案的示例如下所示。

```
**What is a difference between arguments and parameters?**

Parameters are variables in funcion definition.

function add(x, y) { // x and y are parameters
  return x + y;
}

Arguments are variables passed down to a function in place of parameters.

add(2, 3); // arguments 
```

Enter fullscreen mode Exit fullscreen mode

* * *

我每天从路线图中安排一个主要项目。如果我能学到更多，那当然很好，但是这个世界并不完美，我也不完美。这就足够了，不管怎样，我还是在进步。想象一下，除了工作或大学，一年内学习 365 个概念/技术！

重要的是要明确我将在何时何地花时间学习。尽可能容易地将这种常规作为你的习惯。晚上关上笔记本电脑前的最后一件事是打开文本编辑器和浏览器。然后在早上醒来后，做了早上的例行公事，我坐在书桌前学习至少 20 分钟。

2-3 周后，我能够为 Node.js & MongoDB 项目设置 Docker 容器，我可以键入中级 React/Redux & Redux/Hooks 应用程序，并且在我的 Vim/Tmux 工作流方面做得更好(它包含在 bash 脚本中)。我也能够清楚地(这是我得到的反馈)向初学者解释一些重要的 git 和 HTML & CSS 概念(我自己也学到了更多的可访问性)。除了我在日常工作中学到的东西之外，所有这些。

小块化合物。

关于习惯的话题，我推荐詹姆斯·克利尔的《原子习惯》一书，它对我的工作流程有很大的帮助。

额外收获:你可以试着制作 ANKI 卡片来记住你所学的概念。你可以在这里了解更多关于间隔重复的知识。