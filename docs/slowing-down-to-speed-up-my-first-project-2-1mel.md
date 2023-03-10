# 减速加速，我的第一个项目#2

> 原文：<https://dev.to/datalospohy/slowing-down-to-speed-up-my-first-project-2-1mel>

### 现在我们从底层开始了！

这是一个与众不同的夜晚，风速为每小时 60 英里，在我们称之为芝加哥的北极地区造成了华氏零下 16 度的寒风。我坐在电脑前，当它问我是否确定要删除硬盘上的所有内容时，我按下了“是”，然后我在严峻的期待中等待。我从包、库和下载的丛林中解脱出来，但是最困难的部分还在后面，我必须从头开始重建。

[![Burn](img/0e545cdfbc8e4b0931401b084f832e56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cSG6Fqm3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.kym-cdn.com/photimg/newsfeed/000/000/130/disaster-girl.jpg)

**倒带**

*桌面 500 张截图
*1000 个随机文件下载
*100 个随机 app
*现代人所知的每一个 python 包
* RAM 15.99/16
* HDD 978 GB/1000 GB

我心不在焉地过着没有电脑卫生习惯的生活，这一下子让我大吃一惊。我在我的电脑上搜索了一个有三个副本的文件，我分不清 image-1 和 image-18，我会花钱找人找出这个项目的最新版本在哪里。在愤怒的沮丧中，我把一切都推到 git 回购上，犯下了一级纵火罪，一心想要彻底摧毁所有财产，然后看着它燃烧。在现实中，我工厂重置我的电脑，并从底部开始，所以我可以到这里。这是一个令人沮丧的时期，但我在项目的这个阶段学到了三件事，并且都有直接的关联。

**容器化> TDD/BDD >重构**...按照这个顺序。

### 虚拟环境和容器

在第一个项目之夜，一名志愿者问我们组的 virtualenv 在哪里，我们都有点茫然地看着他。他咯咯地笑着说，“永远用一个虚拟的，永远”。在他帮助我们前进的时候，这只是一个随机的边注，但在接下来的一周，当我把我的电脑送回归零地时，它立刻就和我相关了。一旦我准备好重建我的电脑，我在网上查找 virtualenv，第一个结果是为什么我应该使用 Docker。简而言之，docker 容器是为了隔离和可移植性而打包的应用程序。它们远比这复杂，但这个定义符合我的目的。我下载了 docker 和其他 docker 的东西，并创建了 docker 帐户，因为我认为它似乎并不比 virtualenv 好多少。结果是 Docker，可能很棒，在我的第一个项目中过度优化了。这也是一个新的学习世界，同时我还在学习启动这个项目的基础知识。虚拟环境很适合我。实质上，虚拟环境是项目中的一个文件夹，包含所有的依赖项和需求，因此它们不会影响全局设置。一个简单的“pip 安装”和“源激活”,然后我们开始比赛。

[![Over Optimization](img/8810b2bc2e863a43e7a0c622622f97b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XQdkRfjv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/the_general_problem.png)

为什么每个人都应该使用 Docker、虚拟机或虚拟环境？在一个完美的世界里，所有的项目都将使用每个包的相同版本，你永远不用担心兼容性。在我们的世界中，一个程序使用 python2.7，另一个程序使用 python3.6，还有一个程序由于一些愚蠢的原因从未升级到 1.6.1 以上。这些工具，不同程度的成功，允许你在所有这些程序中独立工作。

### 测试驱动&行为驱动开发

当我开始尝试将我的代码插入到 django 应用程序中时，我意识到我应该用一个更简单的程序来练习，看看它正确运行的样子。在找 Django 教程的时候，我偶然发现了一本叫做[服从测试山羊](http://www.obeythetestinggoat.com/pages/book.html#toc)的书。除了这个有趣的标题之外，作者让测试驱动开发看起来像是你在前期投入大量努力的事情之一，然后它会无休止地为自己买单。TDD 是编写失败测试的过程，它评估一段代码应该如何表现，然后编写通过测试的代码。但是，不要一蹴而就，编写最少的代码，让测试朝着通过的方向前进一步。起初，这是一个令人痛苦的缓慢过程，你会觉得没有什么价值，但当我写《第一对夫妇》时，我立即看到了这种需要。我遇到了一个大问题，我有无法测试的代码。当我试图弄清楚是我缺乏测试知识还是我缺乏编码知识时，我意识到我的代码是不可伸缩的。我执行了许多转换和计算，但都没有存储在任何地方。我正在处理越来越大的 CSV，只是将所有内容移入和移出数据帧。 [TDD/BDD 有两个主要租户](https://qualityswdev.com/2010/03/24/the-purpose-of-test-driven-development-tdd/):

1.  可测试代码:可测试对象传递了它们的依赖关系，在阅读时很容易理解。换句话说，不是编写一个使用另一个函数的函数，而是每个函数都有自己的输入并产生自己的输出。这就是所谓的“松耦合”，相反，“紧耦合”是一个相互依赖的功能系统，因此，如果您更改其中一个功能，就会由于功能的重用而产生意想不到的后果。任何人都可以看到这将导致的头痛。

2.  支持重构:简单地说，您可以持续改进您的代码，并确信它仍然以预期的方式运行。

我的代码既不可测试也不支持重构，这让我学到了最后一点东西。

### 重构

这可能有点混乱，因为从技术上讲，我不能重构，因为我必须改变代码的行为。尽管如此，我了解到，设计良好的代码通常要经过多次修改才能得到令人满意的优化。重构是一个系统化的过程，它改进代码，而不创造新的功能，将混乱的代码转化为干净的代码和简单的设计。

### 外卖

这个阶段是关于学习良好的编程习惯。这些习惯不是凭直觉养成的，但却为将来节省了很多时间和麻烦。这是一个跨越多个学科的课程，成功与失败的区别。成功的人通过不断的实践形成并提高生产习惯。我学到了一些构成顶尖软件工程师的好习惯。下一步是将它们融入我的常规流程。我正在以一种更简洁的方式编写我的代码，并引入一个我的代码与之交互的数据库。我正在我的 django 应用中编写一个完整的测试套件。总的来说，这真的是走到了一起，我学到了比我想象的更多的东西。难怪任何有经验的人都会说，你应该总是做新项目来拓展你的技能。

#### 直到下一次