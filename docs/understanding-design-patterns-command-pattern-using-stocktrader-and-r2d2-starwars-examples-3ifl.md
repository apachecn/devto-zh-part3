# 理解设计模式:命令模式使用斯托克拉德和 R2D2(星球大战)的例子！

> 原文：<https://dev.to/carlillo/understanding-design-patterns-command-pattern-using-stocktrader-and-r2d2-starwars-examples-3ifl>

有 23 种经典的设计模式，在原书《设计模式:可重用面向对象软件的元素》中有描述。这些模式为软件开发中经常重复出现的特定问题提供解决方案。

在这篇文章中，我将描述如何使用**命令模式；以及**如何以及何时应用。

## 命令模式:基本思想

> 在面向对象的编程中，**命令模式**是一种行为设计模式，在这种模式中，一个对象被用来封装执行一个动作或在以后触发一个事件所需的所有信息。这些信息包括方法名、拥有该方法的对象以及方法参数的值——Wikipedia
> 
> 将请求封装为一个对象，从而让您可以用不同的请求、队列或日志请求来参数化客户端，并支持可撤销的操作—设计模式:可重用的面向对象软件的元素*

在这种模式中，抽象命令类被声明为执行操作的接口。Command 类定义了一个名为 execute 的方法，它必须在每个具体的命令中实现。这个执行方法是接收器对象和动作之间的桥梁。接收者知道如何执行与请求相关的操作(任何类都可以是接收者)。该模式中的另一个相关组件是 Invoker 类，它请求必须执行的命令。

这个模式的 UML 图如下所示:

[![](img/516ddc8327f414bc9e3671497b7e06f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hS0EiIcp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AwhyKeG0cDMnC6thc.png)

在以下情况下应使用命令模式:

1.  您需要一个具有独立于原始请求的生命周期的命令。此外，如果您想要排队，请在不同的时间指定和执行请求。

2.  您需要撤消/重做操作。可以存储命令的执行结果，以逆转其效果。Command 类实现 undo 和 redo 方法是很重要的。

3.  您需要围绕基于原始操作的高级操作来构建系统。

命令模式有几个优点，概括起来有以下几点:

*   它将调用操作的类从知道如何执行操作的对象中分离出来

*   它允许您通过提供一个队列系统来创建一系列命令

*   实现扩展来添加新命令很容易，并且不需要更改现有代码就可以完成。

*   您还可以使用命令模式定义一个回滚系统，就像在向导示例中一样，我们可以编写一个回滚方法。

*   严格控制调用命令的方式和时间。

*   代码更容易使用、理解和测试，因为命令简化了代码。

我现在将向您展示如何使用 JavaScript/TypeScript 实现这种模式。在我们的例子中，我虚构了一个问题，其中有一个名为 Agent 的类定义了属性:stockTrade 和订单操作。这个类是客户端/上下文和 StockTrader 之间的桥梁。placeOrder 方法负责决定应该执行什么操作。例如，如果 orderType 是 buy 或 sell，该方法应该调用 StockTrader 中的操作。下面的 UML 图显示了我刚刚描述的场景。

[![](img/cf47bcf961eda1595e6844faae0844bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9gHPM1KP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AvHK1Rb4XuwtLAp2z.jpg)

客户端和代理代码如下:

[![](img/f8ff348789c5d50eca2c3b57e34ee882.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DGionQ_h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3080/1%2AWhSV8UxGhu1OtdqN2gaQ-w.png)

最相关的代码味道是 placeOrder 方法，它与 StockTrade 的动作/命令相关联。有不同的技术可以避免这种代码味道。在这种情况下，命令模式是一个很好的解决方案，因为我们希望记录命令的历史。

最后，StockTrade 类如下所示:

[![](img/588afcd1a08da6f7c442ccf1a43ed9b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BG4RI-Qd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2200/1%2AfRyDmFpm5m6w4HabCCFM3g.png)

获得的结果如下图所示:

[![](img/cd51696570167e88d1634086d27ca7ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XCA3eCFu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2558/0%2AdoeDSU0BLE7UJK1R.png)

## 命令模式—示例 1:股票市场—解决方案

将命令从代理类中分离出来的想法是为每个命令创建一组类。然而，这些命令共享一个公共接口，允许我们根据每个具体的命令来执行操作。

这就是我们创建 Order 抽象类的原因，它将有一个名为 execute 的抽象方法。这个方法将从代理类(调用者)中调用。此外，代理类将有一个命令列表来获取命令的历史记录。

通过这种方式，代理将知道必须对其接收的对象执行哪个操作的责任委托给了其他人。主要的变化是代理类不再接收原始属性作为参数(字符串)，因为它没有语义值。相反，代理类现在将接收一个命令对象作为参数，它提供语义值。

使用命令模式的新 UML 图如下所示:

[![](img/8e9817b47e646e2828a84fc8f413e347.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--buh5L4Tp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AI86O_Ct15YukrUH4.jpg)

与客户端关联的代码如下:

[![](img/790236d42dd43dce082d243e59cc8387.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--36-zw9Xm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2472/1%2ApeJVrdECbOi6eWIijzavYg.png)

在这种情况下，每个订单都使用 DI(依赖注入)接收 StockTrade。代理使用 placeOrder 方法调用命令，place order 方法通过 execute 方法执行操作。

与代理相关联的代码如下:

[![](img/7eb57a419452eb8240e0ea78739e7e2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VoEoSMdt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3380/1%2A48q9wNhns-YRtvCEtJj9MQ.png)

您可能会注意到，通过使用 order.execute 方法避免了 if-elseif-else 控制结构，该方法将责任委托给每个命令。

与订单和每个订单相关的代码如下:

[![](img/bf1a938326ddd3ca2526ed15774fa5f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eo694Bmp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2776/1%2AfbHPcbMJcl_oFZegee425A.png)

[![](img/50673c4e3a4b1c7c32d1d6a751bc08be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xm7fFgHe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2400/1%2AURjsIe_7NW3m68_UyhtcZw.png)

[![](img/5a0f70e3382208b1206ae0bd0f679338.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qNJOuCWv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2400/1%2ApZlRXJFVCQRanr451jXn1Q.png)

在这个命令中没有修改 StockTrade 类。因此，在程序执行过程中进行这些修改后的结果如下图所示:

npm 运行示例 1-问题
npm 运行示例 1-命令-解决方案 1

[![](img/6cc283a8be3e6b4b892eddaf4593c180.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k8q9dJZI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2ADN02Yh1E9Kf1qTEvXD2R2w.jpeg)

使用命令模式解决的另一个有趣的例子是当一个机器人有几个命令要执行时。例如，一组命令如 **SaveSecret** 、 **Clean** 和 **Move** 被要求给一个著名的机器人，R2D2。在下面的 UML 图中，您可以看到这种情况:

[![](img/7b9c41ea5395114d0276f6134c35b2da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i_HVJxdV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A6PwzeJ_Tpi7J_eHf.jpg)

与客户端相关联的代码如下:

[![](img/9ed07a71cda5b1867658376cdf1043bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yK80kGDE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3212/1%2AsN2wHLJBfUwPV52YmY5IBQ.png)

在本例中，有三个命令(saveSecretCommand、cleanCommand 和 moveCommand)、两个服务(StoreService 和 R2D2Service)和一个代理(R2D2)。

代理使用 executeCommand 方法调用订单，该方法接收两个参数:1)命令；2)执行前面命令的参数。

因此，与 R2D2 相关联的代码如下:

[![](img/e427a5217d2b5e432cb730146966e282.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---bxL2Lps--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2740/1%2Acak_eABVREn6dy9U6ZXHHA.png)

R2D2 有一个命令列表，可以通过 listCommands 方法列出，并使用 commands 数据结构存储。最后，executeCommand 方法负责调用每个命令的 execute 方法。

因此，下一步是创建与命令(抽象类)和每个具体命令相关联的代码:

[![](img/47d0730d752c54ab8536b761e4528f83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RwCD8qSJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2808/1%2AFJMyGFs5kTh87PJZ4bFSFQ.png)

最后，每个命令调用负责该动作的服务，在这种情况下，我们使用了两个不同的服务来表明不是所有的命令都将责任委托给同一个服务或类。

[![](img/2f3578019309020f306f03dd7cd83ee7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QnHL_D8Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2704/1%2AWezAe4sDM84BnyhysLov6A.png)

获得的结果如下图所示:

[![](img/d5830ab7dadbbea0e0cd28d3f9bb5849.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fnrpMaxm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2236/0%2AJ64_YrQG-KW3x5g9.png)

我已经创建了一个 npm 脚本，它在应用命令模式后运行这里显示的示例。

npm 运行示例 2-命令-解决方案-1

命令模式可以避免项目的复杂性，因为您将命令封装在特定的类中，这些类可以随时添加/删除或更改(包括执行时)。

最重要的事情不是实现我向你展示的模式，而是能够识别这个特定模式可以解决的问题，以及你何时可以或不可以实现所述模式。这一点至关重要，因为实现会因您使用的编程语言而异。

这个帖子的 GitHub 分支是[https://GitHub . com/Caballerog/blog/tree/master/command-pattern](https://github.com/Caballerog/blog/tree/master/command-pattern)

*最初发布于[https://www . carloscaballero . io](https://www.carloscaballer.io/design-patterns-command)2019 年 5 月 23 日。*