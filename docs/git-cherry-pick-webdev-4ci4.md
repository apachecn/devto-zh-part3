# git 精选 webdev

> 原文：<https://dev.to/ashc0des/git-cherry-pick-webdev-4ci4>

用 Chrome 开发者工具来处理一个小 bug、在本地运行应用程序、设置断点和调试代码是非常容易的。分配一个小的用户故事很容易，这个小的用户故事向表单添加一个下拉列表，并且需要修改表单的有效负载，当向 master 提交用户故事的 pull 请求时，要遵循团队中的高级 UI 开发人员提出的实践。这就是我最初着迷的原因。我喜欢前端开发的即时满足感，我喜欢创建可重用的组件。

<figure>[![Guilty as charged.](img/e61e0c4777b37a581ee023a8df30902c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8ijigGuE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.redd.it/4vgo5urck9mz.jpg) 

<figcaption>被控有罪。</figcaption>

</figure>

我从 Java 开始，它是一种面向对象的语言，不像 JavaScript，它是一种动态语言。我的大部分大学课程和项目都广泛使用 Java。我从学习 Java 中获得的编程思维在黑客马拉松和实习中帮助很大，即使他们是 web 开发人员。在我的第一次黑客马拉松中，我尝试了混合应用开发平台 Telerik，令我的团队惊讶的是，我们获得了第一名。我在第一次实习中使用 Angular 1.0，在下一次实习中冒险进入 Node.js 领域，然后在我全职工作的职业加速器项目的第一轮中使用 Polymer，最后在第二轮中使用 React/Redux。

注意到一个共同的主题了吗？

大量的修补工作，大量的 JavaScript 框架，大量的坚持不懈地发布代码，以及大量的 Google 和 StackOverflow。然而，对基本面的关注却神秘地消失了。

换句话说...有人记得学过如何用导数的定义手动找到多项式的导数吗，那有多痛苦？然后在权力法则中寻找甜蜜的救赎？

<figure>[![Slope formula.](img/9ec75d0be71fedb4526554285c0f6b06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ogDSUhbM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://sites.psu.edu/math140/wp-content/uploads/sites/14857/2015/02/12.png) 

<figcaption>不得不做这一切工作。</figcaption>

</figure>

<figure>[![Power rule.](img/af15ab88d97c94731d2a4219cb1c2c1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X-q7VJLT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://8d117786-a-62cb3a1a-s-sites.googlegroups.com/site/julierose3801/introductory-calculus/introductory-calculus/the-power-rule/ThePowerRule..png) 

<figcaption>与这种简单快捷的区分方式相对抗。</figcaption>

</figure>

这有什么关系？当你仔细想想，你会完全理解并欣赏你在使用幂律时所做的事情，因为以前的方法更繁琐。如果你开始用幂法则来微分，你实际上是在走捷径。同样，我一直跳过使用 JavaScript 的幂法则，拼凑解决方案，结果，我对 JavaScript 基础知识生疏了，也不理解普通 JavaScript 的能力和局限性。*为什么反应？为什么是 Node？为什么是 Vue？为什么是骨气？为什么是余烬？*为什么只能在基础知识中找到答案。

回到最基本的我去。🚀