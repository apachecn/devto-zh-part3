# 如何衡量端到端测试的成功

> 原文：<https://dev.to/pcloudy/how-to-measure-the-success-of-end-to-end-testing-2jpo>

[![](img/7c9f16324db0f2bdbb5985d96145e178.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ik5Avpo1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2019/05/end-to-end-testing.png)

进行端到端测试是为了确保应用程序的行为符合预期，并且从开始到结束的动作流是完整的，没有任何问题。举个例子来解释一下，假设你是最终用户，你正在使用这个应用程序预订航班。首先，你要启动应用程序，点击主页上的航班图标。然后，您将输入目的地、日期和其他必需的详细信息。现在，您将选择您喜欢的航班，预订座位，输入乘客详细信息并付款。完成后，您将收到机票，并在您注册的电子邮件 ID 上收到一封跟进邮件。

现在，在这种情况下，如果屏幕需要很长时间来加载下一页，或者如果 book 按钮不起作用，那么这对用户来说将是一种不愉快的体验。因此，我们可以找出从流程开始到结束是否有任何问题。不仅仅是 UI 功能，还有数据加密和安全性、集成到应用程序的其他内部使用平台之间的信息流、防火墙的正常运行等等。这就是端到端测试发挥作用的地方，以确保一切在任何情况下都能正常工作。

**如何执行端到端测试**
在执行端到端测试时，首先要做的是分析需求并建立测试环境。然后，您需要评估主系统和连接的子系统，并定义所有相关系统的职责。列出[测试方法](https://www.pcloudy.com/7-types-of-mobile-app-testing/)和要遵循的标准的描述。一旦完成，您就可以创建测试用例并跟踪需求矩阵。最后，在测试每个系统之前，您需要保存输出和输入数据。

**衡量端到端测试的成功**
为了高效地执行端到端测试并节省时间和精力，应该创建一个矩阵。应该定义一些参数来衡量测试是否成功。这些测量将帮助您定义测试矩阵:

**测试用例状态:**可以通过图表观察到准备好的测试用例的适当可视化，并与最初计划的测试数量进行比较。
**跟踪测试进展:**这种测量将帮助您获得每周完成的测试的细节。跟踪失败的测试和通过的测试将会很容易。
**缺陷细节:**对出现的错误和问题的跟踪应该每周进行一次，并根据优先级跟踪打开和关闭的缺陷。
**环境可用性:**这里我们需要记录在一个环境中运行一个测试所分配的时间，以及执行端到端测试实际花费的时间。

**执行端到端测试的最佳实践**
端到端测试可能非常耗时，我们可以通过以下步骤节省一些时间和精力。在开始测试过程之前，最好提前做好计划。

随着测试用例变得越来越复杂，组件越来越多，调试它们变得越来越困难，缺陷的机会也越来越大。因此，最好执行单元测试和集成测试来解决更容易发现的问题。启动端到端测试后，您还可以开始进行冒烟和健全性测试，以关注高风险场景。
管理环境:在开始测试之前，需要记录需求，并通知系统管理员以满足需求。系统设置应该足够熟练，能够运行测试用例，并使您的试运行环境接近生产环境。
优化:测试完成后，您需要清除测试数据，以便环境恢复，可以再次用于测试。这将使环境准备好随时启动。

**风险分析:**关注至关重要的特性，关注可能导致高风险的失败。准备一个风险评估矩阵，在这些情况下会派上用场。
**限制异常测试:**对异常场景使用低级单元测试和集成测试，对典型用户场景使用端到端测试。

**结论**
随着应用技术的快速发展和 IOT 等技术的日益普及，开发团队必须确保应用具有流畅的功能。在这个竞争激烈的市场中，最终用户可能非常挑剔，因此有必要对应用程序执行[端到端测试](https://www.pcloudy.com/how-to-measure-the-success-of-end-to-end-testing/)，以提供良好的用户体验。