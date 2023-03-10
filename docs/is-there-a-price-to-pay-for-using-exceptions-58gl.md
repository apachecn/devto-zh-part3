# 使用异常需要付出代价吗？

> 原文：<https://dev.to/juanigalan91/is-there-a-price-to-pay-for-using-exceptions-58gl>

## 你有没有想过异常是如何形成并对你的项目产生影响的？它们有用吗？它们会影响性能吗？

在我大学最近的一堂课上，有一个关于异常以及它们如何对你的项目产生影响的具体讨论。大部分的对话涉及到关于异常如何工作，它们什么时候有用，以及它们如何从几个角度影响你的项目的争论；代码可读性、耦合性、性能和异常的总体有用性。我想稍微探索一下这个想法，看看当你开始使用异常时，它们有什么含义。

根据我的经验，异常处理是一个非常广泛的讨论话题，有几种不同的观点，通常是由于每个人的个人经验而形成的。我见过程序员通过对每一个操作或方法执行都做一个 try/catch 来做大量的防御性代码，用一个异常处理把所有事情都包围在一个大的 try/catch 中，直到什么都没有。

# 异常处理有用吗？

是的，在您正在访问可能以关键方式失败的特定资源的场景中，这非常方便，例如，访问数据库、API 调用、打开文件等。由于超时、找不到资源、不符合数据库中的特定规则(如外键或必填字段)或意外错误，这些情况最终会失败。异常允许你从那些场景中恢复，并向用户显示一些东西，一个错误页面，一个没有找到的页面，一个“对不起，请稍后再试”。一些有意义的东西，让用户知道出了问题，而不只是让他们的窗口或浏览器关闭或冻结。

当您正在开发其他团队要使用的库时，或者如果您想为开源做出贡献时，异常也非常有用。通常人们不会花时间去阅读一个特定的库是如何工作的，他们只是使用它。如果他们错过了什么，抛出一个异常将是教育他们并控制他们如何与你的库交互的好方法。

# 但是有时候，会不会有点过了？

另一方面，我们有其他使用异常的场景，这有点矫枉过正，因为你也许可以通过其他简单的控制结构来解决这个场景。

以下面的例子为例:您有一个名为 Employee 的类，它有一个接受 1 个参数(一个名称)的构造函数。假设姓名是必需的，并且您不能创建没有姓名的雇员。所以你决定实现一个自定义异常，当 name == null 时，你把这个异常抛出给任何想要使用它的人，我们称之为 EmployeeException。这意味着:

*   应用程序中想要创建雇员的每个部分都需要将代码放在一个 try catch 中，以捕获特定的错误
*   应用程序的每个部分都依赖于 Employee 和 Employee，这将增加耦合性，从而导致将来的维护问题。
*   try/catch 可能会有点混乱，因为您将抛出一个 EmployeeException。是因为名字是空的吗？或者我还需要设置其他变量吗？还是名字需要有特定的格式？显然，您可以将异常的名称更改为更容易理解的名称，但是如果您在团队中工作或使用外部库，并且其他人编写了该异常，这可能会导致混乱。想象一下，如果有几个错误可能会抛出异常呢？你会为它们中的每一个创建一个定制的例外吗？这可能会增加你的项目和员工之间的联系。
*   这可能会对性能产生影响(我将在后面解释这一点)

我们可以通过向 Employee 类添加一个静态方法来解决这个问题，该方法从一组特定的参数中返回一个布尔值，该值显示是否可以创建 Employee，这将有助于解决我上面提到的问题。这将是异常处理、验证输入和创建验证方法的替代方法之一。

# 我的应用程序性能如何？

对于这次讨论，我想我可以采取更实际的方法。所以我创建了一个简单的 Java 项目，它遵循我之前提到的员工场景。基本上，我们创建一个带有构造函数(字符串名称)的雇员，如果名称为空，我们抛出一个异常。我们做了同样的事情，但是我们在创建雇员之前添加了一个 isValid 方法来检查。我使用 for 循环并多次运行构造函数对此进行了测试，因为为了查看时间执行的差异，我需要创建不止一个雇员。所以我测试了以下场景:

1-用 name = null 创建一个雇员，并用 try/catch 包围它。

[![Create an Employee with name = null and surround it with a try/catch.](img/ed8519ad5ed03530ec58ab307254b6bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4Af1BuPO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3e5vxg6btuw48v1v5u9f.png)

2-创建一个员工，其中一半人会抛出错误，另一半人会通过，并用 try/catch 包围它。

[![second scenario](img/05db22097b9125f932c4bffeea7be3ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QPEsjCdU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ak9vp8AlzWKKAN0jSMB4_LQ.png)

3-创建一个雇员，其中一半人会抛出一个错误，另一半人会通过，并用 try/catch 包围它，同样在 catch 上，我执行 ex.getStackTrace()，只是为了模拟异常处理。

[![third scenario](img/5f4166b6d84645487057aadf3b7552cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FISZ055A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w6cgqz2wi3fs8csbziyh.png)

4-创造一个员工，其中五分之一的人会抛出一个错误，并用 try/catch 包围它。

[![fourth scenario](img/04b0fc4167b65b761bbf2063ae77315e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0JFqdnyN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cdbiovi9i7mizhkrabnx.png)

5-用 name = "test "创建一个 Employee，并用 try/catch 包围它，但是因为它是一个有效的名称，所以没有抛出异常

[![fifth](img/bf4c723b3ce02e89475f7bfaf43897fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--crAN0Yzs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z95mxgifd22xa9r1ooxl.png)

6-用 name = "test "创建一个雇员，但在我执行 isValid 之前

[![sixth](img/d02a6e0ae87d0f0b4404692a73e9c18c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R2Ef5Zsm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8xaiqwh6pedm0fhwedlu.png)

我用 100、1000、10000、100000、1000000 的总数来执行这些场景。结果如下(以毫秒计):

[![results](img/65e877d66abf670545acc272f86c4732.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tcKIadhm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rgbvp0wyvke51r7fot6j.png)

*   对于 i = 100，差别不大，执行时间从 0 到 1 毫秒
*   对于 i = 1000，方案 1、2、3 比方案 4、5、6 多花 3 倍的时间。这开始让人觉得处理一个异常需要更多的时间。此外，从数字 5 中，我们可以注意到，如果没有抛出异常，添加 try/catch 块不会降低性能。
*   对于 i = 10000，场景 1、2、3 比其他场景多花 16 到 23 倍的时间
*   对于 i = 100000，对于场景 1 和 3，它会上升到 136 毫秒和 154 毫秒，对于场景 5 和 6，它会上升到 5 毫秒和 4 毫秒。
*   对于 i = 1000000，抛出和管理异常的场景可能需要 1 秒钟。

# 总结一下！

总的来说，我们可以说异常显然是编程语言的支柱之一。它们使我们能够从几个关键场景中恢复，并允许我们将信息传达给用户。此外，它允许我们跟踪 catch 闭包上的这些错误，因此我们可以从这些错误中学习，并迭代我们的项目来防止它们。

另一方面，我们需要考虑到异常不仅影响最终用户，也影响开发人员。自定义异常会导致代码维护、可读性和性能方面的问题。依我看，这更多的是一个案例一个案例的分析，有时它们会很有帮助，但在其他情况下，它可能会导致一些头痛！在引入异常之前，请确保您稍微考虑了一下。