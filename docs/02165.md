# 帮助新程序员为开源做贡献

> 原文：<https://dev.to/suesmith/helping-new-coders-contribute-to-open-source-30ig>

在过去的几周里，我在 Glitch 上开发了一个应用[，帮助人们学习一点 REST APIs，同时为一个开源项目做出贡献。这是我第一次对一个问题进行具体的探索，这个问题几年来一直萦绕在我的脑海中:我们能为开源做出贡献建立更多受支持的学习途径吗？我想知道这是否可以创造机会，帮助克服许多人在进入科技领域时遇到的一些障碍。🤔](https://glitch.com/edit/#!/api-learner)

# 你有什么？

当您访问开源项目的贡献指南时，经常会发生这样的事情:

*   你知道怎么做吗？
    *   酷，去这里做吧。
*   ...

[![Hulk](img/ef8ec3eaf7b7dd80dea589589cc2abfb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VR_Vs4rh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6666370/58124152-6dc7c480-7c05-11e9-8693-5a0e00d989a6.gif)

在更友好的情况下，可能会有一两个链接将您引导到一些关于如何获得相关编码技能的外部资源——在许多情况下，会有不需要编写代码的贡献选项。但是我的教育者(和学习者)总是觉得错过了将贡献和代码学习结合起来的机会。

虽然一些项目包括关于他们生态系统中用户开发的全面的教程材料，但它更普遍地关注于插件、扩展等等——而不是对核心代码库的贡献(无论是以错误修复、功能开发还是其他形式)。虽然可扩展系统是对社区产生影响的重要途径，但是开发一个扩展可能比将您的代码与现有的代码库集成在一起需要更少的协作。

[![Audrey](img/816bd135d87ebd8ef2c27c56309e2ad1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BNFee2H2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6666370/58124203-8e901a00-7c05-11e9-9883-741338633c29.gif)

我经常看到人们(在我看来是正确的)谈论开源贡献是一种很好的学习方式，但是[即使对于有经验的开发人员来说也是一种艰难的练习](https://dev.to/marcel_cremer/why-your-open-source-project-might-lack-my-contribution-1-4m8g)，对于新的编码人员来说通常不是很容易理解- ***，并且倾向于不被支持作为一种明确的学习渠道*** *(与我们在开发人员工具文档中看到的教育支持类型相比)*。

我们基本上有这些途径:

*   从专门的教育资源中学习
*   通过阅读开源代码来学习
*   离开并获得编码技能，然后回来为开源做贡献

难道我们不能消除离开的需要，而是将学习与贡献结合起来，至少在某些情况下，为人们带来机会吗？我绝对没有贬低开源维护者的责任(和债务)的意思，但是增加这些支持难道不是对构建贡献者社区的一种投资吗？

# 教育 vs 专业软件工程

当我学习编码时，我课程的最后一部分是从事一个实质性的软件项目。到目前为止，我们编写的大部分代码都是在小的独立应用程序上——换句话说，你是每个代码库的唯一开发者。我无意中选择了一个需要扩展现有系统的项目——当时我不知道这会带来一系列完全不同的挑战(也就是说，我要做的第一件事就是修复大量的错误😑).我当时没有意识到的是，这更接近于现实世界的开发过程——将我的工作与他人的代码集成在一起。

[![Buster Keaton](img/97b849fc83f9f3664f59b13d70032958.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--av9CdP_B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6666370/58124441-242ba980-7c06-11e9-8542-914f635ef7fc.gif)

在过去的几年中，围绕成功的专业软件工程，除了能够很好地编写代码之外，还需要多种技能，这已经有了很多(在我看来是健康的)讨论。我不能代表所有的编程教育项目(也许从我 2006-2007 年的课程开始，事情已经发生了变化，希望不是每个人都有在考试时在纸上写代码的超现实经历🙃)，但是看起来我们仍然将这种学习推迟到新的开发人员进入工作场所。

我已经在技术教育领域工作了十多年，我可以证明创建符合一系列期望学习目标的体验的挑战[而不是过度做作](https://devrel.net/developer-experience/making-the-implicit-explicit) -但现实是，我们的许多编码教育都缺少作为团队开发人员工作的一些关键日常元素。

[![Ralph](img/1465548c9747f943052bba16857385a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f6nWZj4V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6666370/58124534-5a692900-7c06-11e9-809f-27cc6b08dcfc.gif)

一些大学、学院和其他课程为学生提供了在现实世界应用程序中工作的机会，然后还有实习- **但是很多人没有获得这种类型的学习机会**。另一方面，开源项目，就其本质而言，涉及到社区——它们是人们学习开发和协作/交流技能的潜在渠道吗，这些技能可以启动他们的简历并帮助获得工作机会？🏆 🚀 💰

[![Saga](img/8fbc31f61cf4462983a28e6a69468bfa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2nMHuRPl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6666370/58124665-a87e2c80-7c06-11e9-9141-9d1abbf3f051.gif)

# 学习和贡献途径

好吧，假设我们试图通过贡献来支持学习——这在实践中会是什么样子？诚实的回答是我不完全确定，因为这可能很复杂，并且很大程度上取决于单个项目的具体情况。

当谈到用开发者工具促进学习和成功时(即开发者关系做什么)，我发现定义贡献途径是有帮助的——你希望你的社区如何能够参与你的软件(除了作为用户)。

[![George](img/7054b2ea34f6c4d0c3faeb376a592682.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AJcpSDDI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6666370/58124733-d2375380-7c06-11e9-8e57-67cd71e1af4d.gif)

那么，我们将努力帮助人们学习什么呢？在专业软件开发中，有哪些技能是传统教育模式中缺失的，但开源贡献可能包括的？

*   合作
*   沟通
*   代码审查
*   版本控制
*   还有别的吗？

我们可以使用哪些类型的资源来支持这种学习？

*   文档(例如教程、参考资料、示例代码)
*   支持(当然，这是一个劳动密集型的选择，但是它也可以为项目相关人员创造获得指导经验的机会)
*   到现有资源的入口点和链接创建引导路径，可能通过交互式控件针对个人背景进行定制
    *   *这可能包括外部资源——但是，如果项目已经被很好地记录在案，这也会有所帮助*😉
*   社区计划(例如，指导计划，鼓励现有贡献者记录他们自己的过程，等等)
*   还有别的吗？

> 有大量奇妙的通用开源指南可用(例如，[开源指南](https://opensource.guide/)、[仅限首次使用](https://www.firsttimersonly.com/)、[待价而沽](https://up-for-grabs.net/)、[我的第一次公关](https://my-first-pr.github.io/))，但它往往不是特定于项目的(专注于 Git 工作流的[教程](https://medium.freecodecamp.org/demystifying-open-source-contributions-c60fe2bde6d0)等)。在项目层面上，有一些很棒的[贡献指南](https://github.com/atom/atom/blob/master/CONTRIBUTING.md)(模板、项目架构的解释者、标记好的第一个问题、首选代码风格等)，但是我还没有遇到任何足够全面的内容，可以作为一个独立的资源帮助人们开发他们的贡献。

几年前，我运行了一个项目，帮助人们获得开源技术和协作技能，例如通过向 GitHub repos 投稿(不一定包括代码)。完全没有技术背景的人对 GitHub 等工具的直觉让我大吃一惊，所以我对人们通过单一过程学习各种技术导向技能的潜力普遍持乐观态度。

[![GitHub Contributions](img/9b9288a8d2c6181008fa75e79f0ebb13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ecp-tcjT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/6666370/58126662-55f33f00-7c0b-11e9-9eef-d4bc52057c10.png)

随着 Glitch 这样的平台的崛起，现在似乎是一个雄心勃勃地帮助人们学习的好时机。未来几代人(和未来科技)的成功取决于每个人的能力——用 Mozilla 的话说——读、写和在线参与。为开源项目做贡献会让你成为利益相关者- ***，有了开源这样一个 web 技术不可或缺的部分，这些社区应该是整个社会更真实的代表*** 。

# 你怎么看？

好吧，对于新程序员来说，参与某些代码库是很困难的，我当然不是说这在任何情况下都是一个容易的学习经历——但是我不禁觉得，如果一个项目从一开始就考虑到外部的贡献，那么就应该有可能扩大能够参与这一水平的参与者的范围。

[![Crucifixion](img/503c7a4b9c3e259ed67e1e796bfcbdd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rOKLUttp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6666370/58125655-f4ca6c00-7c08-11e9-9f8a-34c8327541c5.gif)

我们在开发人员教育(至少是软件产品)方面正慢慢变得更好，通过例如链接到支持材料来为经验不足的学习者敞开大门。然而，尽管有比以往更多的代码学习资源可用，软件工程的准入门槛越来越高(特别是前端 web 开发，这曾经是学习第一编码技能更容易的地方)。

作为软件产品所有者，如果你负责培养一个支持开发工具新手的社区，为什么我们不能为主要代码库做同样的贡献呢？

[![Good Place](img/c7a27dd089557cdfe23cd323a021d45c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XS9BlWSQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6666370/58125706-1166a400-7c09-11e9-9f17-1e8b0b2edf7d.gif)

从事真实世界的软件项目需要我的技术技能——从我的课程中的编码练习上升到一个层次，而从事开源项目又把我带到了另一个层次。起初，我发现在户外工作的前景非常可怕，但事实上，我发现这是一次令人难以置信的解放和建立自信的经历。它也把我对编码的看法从相对孤立的追求转移到了本质上面向团队的东西(令我惊讶的是，我喜欢它的这一方面😂).我希望我们能给更多人那种协同工作软件开发的感觉。

我很想知道人们对这种方法有什么看法？你已经试过了吗？如果你认为它在实践中太难实现，那么，你可能是对的，但由于学习者一直对此感到惊讶，我真诚地相信它值得探索！T3】🙏 📣