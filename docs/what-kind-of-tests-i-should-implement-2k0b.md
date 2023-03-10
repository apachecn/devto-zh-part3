# 我应该实现什么样的测试？

> 原文：<https://dev.to/nbentoneves/what-kind-of-tests-i-should-implement-2k0b>

# 我应该实现什么样的测试？

试验...测试和更多的测试！那么，我应该实现什么样的测试呢？我需要多少测试？当一个开发人员需要实现一个用例，一个新特性，创建一个库或者一个服务时，这些是他经常问自己的问题。

首先，有几种你可以应用的软件测试策略/技术。它们可以结合在一起，通常在你的开发周期中你会有不止一种这样的技术。

*   自动化测试
*   箱式方法
*   软件性能测试
*   可用性测试
*   安全测试

## 关卡

<figure>

[![Resultado de imagem para levels testing](img/d90c54d155aded8395ecf1770d3fb965.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dIRZcHbp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.professionalqa.com/asseimg/Software-testing-levels.png)

<figcaption>The four basic levels of testing - (professionalqa.com 2019)</figcaption>

</figure>

现在我们将分析等级。重要的是要记住，这些级别有时有其他名称或其他称号。(此列表不包含所有类型的测试):

*   单元测试:这是测试生态系统的一小部分，第一部分。通过单元测试，您可以评估实现的代码。它非常灵活，允许检测简单的缺陷，比如异常或计算问题。
*   **组件测试:**在这部分测试中，您可以测试功能或流程，但所有外部依赖项都被模拟/模仿。在这一级，我们不想知道依赖项 X 或 Y 是否返回了正确的结果，我们必须忘记外部结果并测试我们的组件行为和内部通信。
*   **集成测试:**用外部依赖的真实结果测试行为。这种级别的测试有时会变得难以维护，因为其他依赖项应该一直可用。你应该讨论这种水平的测试是否会给你的项目带来好处。
*   系统测试:有时称为端到端测试，这是您测试完全集成的地方。这种类型的测试确保软件在所有预期的目标系统中工作。这意味着您应该配置一个系统，并且测试需要在本地环境之外部署的应用程序中运行。
*   **验收测试:**也称为用户验收测试，这类测试用于获得客户认可。批准后，软件可以交付。

总的来说，我使用单元测试、组件测试、系统测试和验收测试。我相信有了这四个级别，你就可以达到一个很好的测试覆盖率，并且它们会防止你出现一些问题。

你通常使用哪种水平仪？你对这些水平中的一个有负面的体验吗？复制下面的项目符号，让我们分享我们的观点:)

*   我最喜欢的测试级别是什么，为什么:
*   我最糟糕的经历是什么，为什么:

[原帖](http://mydevlife.azurewebsites.net/2019/04/23/what-kind-of-tests-should-implement/)

参考资料:

*   [https://www.qasymphony.com/blog/functional-testing-types/](https://www.qasymphony.com/blog/functional-testing-types/)
*   [https://www . guru 99 . com/software-testing-career-complete-guide . html](https://www.guru99.com/software-testing-career-complete-guide.html)
*   [https://www.softwaretestingmaterial.com/software-testing/](https://www.softwaretestingmaterial.com/software-testing/)
*   [http://www . ijera . com/papers/vol 4 _ issue 4/Version % 209/s 440999102 . pdf](http://www.ijera.com/papers/Vol4_issue4/Version%209/S440999102.pdf)