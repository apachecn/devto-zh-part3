# 重构:保护子句

> 原文：<https://dev.to/carlillo/refactoring-guard-clauses-4ee6>

> 在计算机程序设计中，一个保护是一个布尔表达式，如果程序要在所讨论的分支中继续执行，它的值必须为真。
> 
> 无论使用哪种编程语言，保护代码或保护子句都是完整性检查的先决条件，用于避免执行过程中的错误。—维基百科

未应用保护子句技术的代码中出现的主要问题如下:

1.  *过度缩进。*如果过度使用嵌套的控制结构，则意味着存在高级别的缩进，这使得代码阅读困难。

2.  *if-else 之间的关系。*当 if-else 之间有大量分离的代码片段，且这些代码片段在概念上相互关联时，需要通过在不同部分之间跳转来执行代码读取。

3.  脑力劳动。源代码中不同跳转的结果导致在代码生成中产生额外的工作。

所以，保护条款的实际应用是以下情况:

[![](img/4832ca37e97cc9d8bf5f7adbb7581a0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DOFUAWr2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2336/1%2ALn_KZbLUoE2dkyk8IZmGDA.png)

在这种情况下，大多数时候，您必须颠倒逻辑以避免使用保留字 else。前面的代码将重写如下:

[![](img/760db665bdef3f0f69883f7052bab0c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ezdRGjpg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2808/1%2ABXyrgvT3P0GHsFf87w90KQ.png)

因此，导致退出该方法的特定情况将被放置在该方法的开始，并以避免继续通过该方法的令人满意的流程的方式充当守卫。

通过这种方式，该方法易于阅读，因为特定的情况在相同的开始，并且令人满意的流量使用的情况是该方法的主体。

有些人反对保护条款，认为每种方法中应该只有一个出口点，而使用这种技术，我们可以找到几个出口点。它不应该与无处不在的回归和不受控制的方法相混淆，这将使我们付出更大的精神努力。但是，所有的回报都是明确控制的，因为他们会在警卫或在方法结束时发现。

下面我们将看到更复杂的保护条款的例子，其中代码的阅读和理解得到了很大的提高。

[![](img/ae3e75e9ac3dc89d95acbb358b43d10e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T5nPew9f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2Aibg0XxDSo2KOiyrK.jpg)

假设您必须创建一个方法来计算健康保险的成本，其中用户 ID 作为一个参数被接收。使用该 ID 在数据库中进行搜索以检索用户。如果用户不存在，将抛出一个名为 UserNotFoundException 的异常。如果用户存在于系统中，下一步是验证用户的健康保险是否对应于对该算法有效的保险之一:Allianz 或 AXA。如果保险无效，必须返回一个名为 userpinsurancenotfoundexception 的异常。最后，这个算法只对西班牙国籍的用户有效。因此，您应该再次检查用户是否是西班牙人，以执行保险计算或返回一个名为 UserIsNotSpanishException 的异常

[![](img/c3d5a13a2c542683d94c2a9f5a4bb30c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y3BRGLsM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2976/1%2AdqkCnApeMMb4h01St55AYw.png)

如你所见，代码有许多缩进层次。下面显示了先前算法的相同版本，但是应用了保护子句技术。这种技术使得代码更具可读性。请注意，已经应用了 3 个保护子句，允许生成不干扰算法结果的替代路径(抛出异常)。

[![](img/9e9e914b01f9c1f6337888209a59ef6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QnE7DCSk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3008/1%2ABlk6XGgmhW8lMB6g-ZlveQ.png)

一些必须解决的问题:

1.  为什么没有 if-else if 的情况？

2.  别想了！如果你的代码需要类似 else 的案例，如果这是因为你打破了*单一责任*的原则，并且代码做出了更高层次的决策，那么应该使用诸如划分为子方法或设计模式之类的技术来重构，比如[命令](https://carloscaballero.io/design-patterns-command/)或[策略](https://carloscaballero.io/stategy-pattern-in-javascript-typescript/)。

3.  负面条件不太好理解。

4.  为此，我们有另一种叫做方法提取的重构技术，它包括将代码提取到函数中，以便重用或阅读理解。在下面的示例中，我们修改了前面的示例，创建了一些方法，以便更好地阅读和理解代码。

在使用子句 guard 时，条件的逻辑通常是颠倒的，并且根据条件的复杂性，理解该条件中所评估的内容是相当复杂的。

这就是为什么在小函数中提取条件的逻辑是一种好的做法，这样可以提高代码的可读性，当然也可以发现其中的错误，因为评估条件的责任被委托给了特定的函数。

对于我们的医疗保险示例，我们可以生成以下方法:

[![](img/09060cc80b55deaea11502db4d556abb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--prtzWRuw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2608/1%2AlareEoroDPWZ3wJb-MsFgw.png)

没有必要创建一个函数来检查用户是否存在，因为只需检查用户是否为空或未定义就足够了。因此，生成的代码如下所示:

[![](img/7dffdbdedc8e83f5503a387c03dc3e56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P6LH4Bjs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2608/1%2AHvFR_kusMb-rNHPin6zFoA.png)

有许多做法是为了提高代码的质量。在应用重构技术时，要学习的最重要的事情是，它们应该集中在两点上，主要是:

1.  *解耦代码*，它允许小的变更不会在整个软件项目中引起大的连锁变更。

2.  *可读性*，非常重要的一点是，开发人员要明白，他们工作的大部分时间都是基于阅读代码，而且很可能是其他开发人员编写的代码。开发人员不花时间理解基本逻辑是非常有益的，因为它不容易阅读。

重构从最基本的点开始，一个简单的如果，到一个架构模式。关注我们软件开发的所有方面是很重要的。

[Refactoring.com](https://refactoring.com/catalog/replaceNestedConditionalWithGuardClauses.html)
[卫队—维基百科](https://en.wikipedia.org/wiki/Guard_(computer_science))

*最初发布于[https://carloscaballero . io](https://carloscaballero.io/refactoring-guard-clauses/)2019 年 5 月 31 日。*