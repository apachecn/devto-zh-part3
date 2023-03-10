# 从瀑布式测试到敏捷测试，我学到了什么？

> 原文：<https://dev.to/lambdatest/what-i-learned-while-moving-from-waterfall-to-agile-testing-245n>

我仍然记得我们的交付经理宣布从下一个阶段开始，项目将变得敏捷的那一天。在参加了一些培训和做了一些在线研究之后，我意识到作为一个传统的测试人员，从瀑布式测试团队转移到敏捷测试团队是促进我职业生涯的最好的学习经历之一。在敏捷测试中，有一定的挑战，我的角色和责任增加了很多，工作场所要求前所未有的速度。除了帮助我学习自动化工具以及提高我的领域和业务知识，它还帮助我接近团队并积极参与产品创作。在这里，我将分享我作为一名传统测试人员从瀑布到敏捷所学到的一切。

## 敏捷中的测试与传统测试有何不同？

当测试人员从瀑布项目转移到敏捷项目时，他们学到的第一件事就是传统测试和敏捷测试之间的明显区别。在项目计划、执行和团队中测试人员的参与方面，可以清楚地看到这种差异。我们来看看细节上的区别。

[![](img/857b2c305ad60126c7a05c9a68dbab2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mAqm1PzV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.pinimg.com/originals/af/82/12/af8212b4fb91543e89d4b13dc1ce5e84.jpg) 

*[图片来源](https://www.pinterest.com/pin/191051209162779054/)*

### 基本意识形态

在传统的软件开发生命周期中，项目的主要原则是只有在缺陷被修复后才发布应用程序。然而，敏捷处理迭代方法，在每次迭代中，测试人员必须检查质量标准。最近，[左移测试](https://www.lambdatest.com/blog/how-shift-left-testing-can-help-your-product-quality/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)的采用明显加快了敏捷测试的速度。

### 流程如何运作？

在传统的瀑布方法中，测试人员在项目开始时收集需求，并在开发结束时再次收集需求。截止日期保持不变，如果开发团队延长他们的截止日期，测试持续时间会变短，跳过一些重要的测试阶段。

然而，在敏捷测试中，开发和测试被包含在每个阶段。测试人员在每个 sprint 中都与开发人员一起工作，因为它要求更快的交付，手动测试在许多场景中被自动化测试所取代。

### 团队如何运作？

瀑布方法在很大程度上依赖于指定需求的文档。验收测试通常由涉众或最终用户完成。

另一方面，敏捷高度依赖于项目中每个人之间的交流。验收标准是在用户故事中定义的，因此，验收测试是由测试人员完成的。除了手工或自动化测试，测试人员必须精通多个领域。以下是高效软件测试人员的 17 项顶级技能。

### 软件发布如何变得成功？

软件的成功或失败矩阵取决于测试如何进行。如果在任何情况下，出现了一些严重的缺陷，项目就别无选择，只能进入红色区域。

[![](img/951fa707c1fe0d6828b71a353188fde4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nKkAr2HJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dumielauxepices.net/sites/default/files/software-development-clipart-final-project-771569-7789703.png) 

*[图片来源](https://dumielauxepices.net/sites/default/files/software-development-clipart-final-project-771569-7789703.png)*

在敏捷中，持续的反馈被提供，演示与涉众一起被安排，当截止日期临近时，减少任何关键依赖的范围。

## 我在敏捷中开始测试时发现了什么？

除了新的工作文化和学习测试之外的很多新东西，当我加入敏捷测试团队时，我还发现了很多新的东西。

### 每日站立会议

在我以前的项目中，每天或每周的会议通常是关于目标讨论、团队中的任何新变化或者经理是否想与我们分享任何信息。在敏捷中，我印象最深的是每天的站立会议。

*   站立会议通常每天早上进行 15 到 30 分钟。
*   持续时间因团队规模而异。
*   每个团队成员都会被问 3 个问题

    *   前一天你做了什么？
    *   今天你会做什么？
    *   有什么阻碍你交付成果的障碍吗？
*   参与者包括整个团队，包括涉众和 scrum master。

*   会议的主要目的是明确整个团队的进度，并消除任何障碍。

*   要解决任何障碍，scrum 大师必须负起责任。如果超出了他的范围，他应该确保其他人必须承担责任。

[![](img/237c89aebfd277603ab6733d71cc368e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sH4Zfl8f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://keyles.com/wp-content/uploads/2017/02/Dilbert-2017-02-06.png) 

*[图片来源](https://keyles.com/wp-content/uploads/2017/02/Dilbert-2017-02-06.png)*

### 测试程序分为 4 个象限

在从瀑布模式转向敏捷模式的过程中，最让我惊讶的是，整个测试过程被分成了 4 个象限。

#### ***象限 1***

这作为检查代码质量的初始安全测试过程。测试人员提供即时反馈，基于此，开发人员继续他们的工作。它包括

*   [单元测试](https://www.lambdatest.com/blog/automated-testing-with-junit-and-selenium-for-browser-compatibility/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)检查一段代码是否满足需求。
*   对组件架构进行测试，以确保它们在组合在一起时能够正常工作。

#### ***象限二***

第二象限主要是业务驱动的。测试人员得到需求，这样就可以毫无障碍地开始编码。开发人员开始他们的工作时要牢记业务目标。它包括

*   [线框或原型的用户体验测试](https://www.lambdatest.com/blog/27-open-source-web-ui-mockup-tools/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)。
*   测试工作流的例子和可能的场景。

#### ***象限三***

第三象限的目的是实现 Q1 和 Q2 的目标。自动化测试用于评估，牢记产品的实际使用。即使产品尚未完成，演示也是为了确保开发是基于业务需求进行的。它包括

*   协作测试
*   用户接受度测试
*   [探索性测试](https://www.lambdatest.com/blog/exploratory-testing-all-about-discovery/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)
*   [可用性测试](https://www.lambdatest.com/blog/what-is-usability-testing-and-why-you-need-it/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)
*   [成对测试](https://www.lambdatest.com/blog/leveraging-pairwise-test-technique-for-cross-browser-testing/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)包括利益相关者或客户。

#### ***象限 4***

这主要包括测试非功能性规范，如安全性、性能等。在最终交付成品之前，测试人员使用这个象限来检查预期的非功能性质量。它包括

*   测试基础设施和数据迁移
*   测试负载和可伸缩性
*   测试基础设施
*   性能和压力测试
*   安全测试，以确保认证和黑客攻击的预防措施。

### 制定策略是敏捷测试的关键

当我们计划从瀑布式测试转移到敏捷测试时，保持一个合理的策略来帮助事情以一种有组织的方式进行是非常必要的。敏捷测试策略通常包括 4 个阶段。

#### **T3】迭代 0T5】**

在此阶段，初始设置任务完成。这包括安装工具、需求分析和确定执行特定任务的资源。

*   商业案例已经建立
*   需求被分析，用例被创建。
*   风险分析结束
*   计算成本估算后，准备一个初步项目。

#### ***发展阶段***

这是项目中最重要的部分，因为大多数测试程序都是在这个阶段执行的。

*   在每个 sprint 中，团队实施敏捷建模、Scrum、敏捷数据和其他敏捷实践。
*   需求按照优先级排序，团队在每个 sprint 中执行优先级最高的测试。
*   测试分两个阶段进行。开发人员测试，即开发人员完成编码后进行的测试。他们验证服务集成测试和单元测试。敏捷验收测试是由测试人员执行的。
*   在敏捷验收测试中，不仅项目的测试团队，而且来自涉众的测试团队也一起执行测试用例。

#### **T3】产品部署 T5】**

产品被部署到生产中。该团队开展多项活动，如

*   培训最终用户
*   备份数据
*   营销该版本
*   最终用户和系统文件的记录。

#### ***生产支持***

产品进入生产阶段

*   如果添加了任何新模块，则进行定期测试。
*   如果出现任何错误或用户查询，问题将由生产支持团队处理。

### 从一开始就成为 SDLC 的一部分对于敏捷测试来说是不可或缺的

当我在一个遵循瀑布模型的项目中工作时，测试人员在所有事情上都有点落后。他们只是在 2004 年参与了这个项目

*   需求收集阶段，在客户交付最终的软件需求规格之前。一旦交付，我们必须分析需求，并在有任何疑问的情况下联系利益相关者或 BA。
*   开发阶段完成后。我们必须测试模块，并在 QA 工具中报告错误。

这种方法的主要缺点是不恰当的协作和狭窄的测试窗口。

*   开发人员和测试人员之间没有协作或适当的交流。测试人员的工作是在开发人员所做的艰苦工作中发现错误。
*   测试人员没有得到任何适当的时间框架。如果开发团队延长了他们的截止日期，测试人员不得不缩短他们的截止日期并跳过一些重要的测试阶段。

然而，敏捷中的测试要求测试人员和开发人员从一开始就一起工作，并且双方都必须做对方的部分工作。

### 用自动化加速敏捷中的测试

敏捷开发就是开发正确的产品，并降低开发过程中的相关风险。变化总是受欢迎的，为了控制时间复杂度，敏捷测试需要自动化。除了[视觉回归测试](https://www.lambdatest.com/smart-visual-ui-testing?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)和可用性测试之外，大多数其他测试程序，比如单元测试、功能测试，现在都被自动化了。

这引导我们学习新的工具，如硒，UFT，Appium 等。测试人员还必须学习 CI/CD 工具，如 GitLab、Jenkins、Codeship 和其他工具，以保持在行业中的领先地位。这让我意识到，当我从瀑布模式转向敏捷模式时，测试变得更具挑战性，这给了我一个磨练测试技能的机会。

### 更好地理解业务逻辑

为了[编写有效的测试用例](https://www.lambdatest.com/blog/17-lessons-i-learned-for-writing-effective-test-cases/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)场景，特别是当涉及到探索性测试时，一个优秀的敏捷测试人员必须对领域应用程序如何工作有正确的认识和理解。当我成为敏捷团队的一员时，我能够与开发团队更紧密地合作。我对开发术语越来越熟悉，探索并获得了更清晰的架构图，并帮助创建创新的业务案例场景。

为了创建覆盖面更广的更少的测试用例，测试人员最终需要对业务逻辑有一个清晰的概念。这需要与开发人员和业务分析师进行及时的讨论，阅读规范，并使用应用程序。最终，这不仅增加了他们的技术知识，还增加了他们的领域和业务知识。

## 从瀑布测试转移到敏捷测试之前的先决条件

除了愿意学习和准备深入业务之外，在从瀑布式测试转向敏捷测试之前，我需要学习的东西很少。

### 自动化工具

敏捷要求速度。我再也不能浪费时间在每天、每周或每次冲刺的重复测试上了。有人呼吁加快严格的回归测试。我意识到为了成功地从瀑布式测试转移到敏捷测试，我必须学习自动化工具，例如

*   [**Selenium web driver**](https://www.lambdatest.com/blog/selenium-webdriver-tutorial-for-cross-browser-testing/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)——最大限度减少自动化网站测试的[挑战的终极开源测试自动化工具](https://www.lambdatest.com/blog/common-challenges-faced-during-website-automated-testing/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)。
*   **惠普 UFT**–用于执行功能测试
*   **Appium**–用于测试移动应用

还有。单元测试和 BDD 测试工具，如 JUnit、Pytest、JBehave、Cucumber 等。

### 项目管理工具

惠普质量中心用于报告和跟踪缺陷的日子已经一去不复返了。在从瀑布式测试转向敏捷测试之前，我们必须学习[顶级协作工具](https://www.lambdatest.com/blog/top-19-collaboration-tools-for-your-software-testing-team/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)，它们有助于[错误报告](https://www.lambdatest.com/blog/advanced-guide-on-writing-a-bug-report/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting)以及项目管理，比如

*   松弛的
*   JIRA
*   螳螂

### 跨浏览器测试是处理浏览器差异的关键

随着开发与每个 sprint 的测试相结合，我意识到一个网站可能在不同的浏览器上看起来不同。测试数百个浏览器可能非常耗时，并且可能耗费大量基础设施和带宽。

然而，有一些基于云的浏览器兼容性测试工具，比如 LambdaTest，你可以使用它们对运行在不同设备上的 2000 多个真实浏览器进行测试。LambdaTest 还通过他们的[云 Selenium Grid](https://www.lambdatest.com/selenium-automation?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-010419eu&utm_term=OrganicPosting) 提供基于 Selenium 的自动化测试，以帮助您在数百种浏览器-设备-操作系统组合中最大化您的应用程序的测试覆盖率。

## 我在敏捷测试中面临的挑战

因为方法不同，有很多新的东西需要学习，学习和实现这些东西对我来说有一定的挑战

### 学习编程语言&开发程序

在敏捷中，没有专门的术语叫做测试人员或开发人员。开发人员必须进行部分测试，测试人员也必须进行部分开发。由于敏捷要求快速交付，手工测试的空间很小。基于这个项目，我们必须学习很多自动化测试工具以及编程语言，比如 C#或 Python。测试人员还必须学习使用部署和集成工具，如 Git、Jenkins 等。

### 小期限

更快的交付意味着更短的期限。每个 sprint 和用户故事都有一个非常严格的期限，团队必须在此期限内完成开发，执行所需的测试场景，并与利益相关者或管理层一起安排演示。即使在满足了所有需求之后，如果涉众不满意，我们也必须关注他们的关注点并解决问题。

### 需求频繁变化

敏捷对于涉众来说是灵活的，因为如果他们对团队交付的东西不满意，它给了他们修改需求的自由。然而，在某些情况下，这对于开发产品的团队来说是不利的。

假设正在开发一个网站，根据需求创建了交互式导航。然而，当演示被安排时，利益相关者说它看起来不太好，他们想要一个视差导航。在这种情况下，开发团队必须花费额外的时间来创建导航系统，测试人员必须测试它。最终导致前功尽弃。

### 无缝协调

多个团队同时交付项目的各个方面。协调是释放每个团队最大潜力的关键。协调有助于以更快的速度推动开发和测试，并在团队中实现更好的透明度。

## 结论

作为一名测试人员，从瀑布式测试转移到敏捷测试可能具有挑战性，并且一开始看起来可能有点可怕。然而，它赋予你巨大的学习能力。如果你在敏捷中进行测试，那么每天的工作都会给你带来很多要学习的东西，建议和实现你的创新想法的范围，最终，在行业中前进。就像我一样，如果你是敏捷测试的新手，不要害怕或困惑。抓住机会，从每个机会中学习。在项目中展示你的技能和创新，这将有助于你推进你的职业生涯，成为一名熟练的手工人员和一名熟练的自动化测试人员。

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://accounts.lambdatest.com/register/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-280319eu&utm_term=OrganicPosting)