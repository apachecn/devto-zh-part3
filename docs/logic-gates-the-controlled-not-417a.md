# 逻辑门:受控非

> 原文：<https://dev.to/tttaaannnggg/logic-gates-the-controlled-not-417a>

今晚，我哥哥给我讲了一个特殊逻辑门的故事，那个**控制的不是**。

受控非门是一个接受两个输入的门。如果第一个输入为 0，第二个输入不受影响，并作为我们的输出发送。如果第一个输入为 1，我们在将第二个输入作为输出发送之前将其反转。

很整洁，是吧？也许需要花一分钟思考如何从基本逻辑门(非/与/或)实现。

我已经被宠坏了，但这是我的解决办法。

之前，我已经创建了一个多路复用器。一个多路复用器可以这样描述:有三个输入，A，b，S，S 是选择器，决定我们输出 A 还是 b，如果 S 为 1，我们输出 A，如果 S 为 0，我们输出 b。

它被表示为`(A*S)+(B*~S)`，看起来像这样:

[![](img/cd068bb90be4b33b5d8cf9fa9672ae3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DRPFDV7U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zefvmvvvjhy59qxb77xu.png)

让它适应我们的用例非常简单。我们只需要用 A 和~A 代替 A 和 B，当 S 为真时(当我们的控制器打开时)，我们要翻转我们的 A，所以我们将那个操作表示为`(~A*S)`。当 S 为 false 时，我们希望输出我们的 A 不变，这意味着我们应该将它插入到以前是我们的 b 的位置中，这看起来像这样:`(A*~S)`。

合在一起，我们得到`(~A*S)+(A*~S)`，或者更简单地说，`(NOT(A) AND S) OR(A AND NOT(S))`。看起来是这样的:

[![](img/23b7f9e6e14ed541e625bccba974b6fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mbs6uloV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w0bs1frpzllpqdimrfvr.png)

注意到什么了吗？如果没有，我们将做一个快速真值表。

```
A S | OUTPUT
-----------------
0 0 |   0
0 1 |   1
1 0 |   1
1 1 |   0 
```

Enter fullscreen mode Exit fullscreen mode

我们给它取了个名字，叫异或。

我们的结论是:即使在最低、最简单的层面上，我们可以用来尝试对我们周围的世界做出真实的声明——即使我们已经把一切都简化为以 100%可预测的方式表现的 1 和 0 的集合，我们仍然在围绕我们的数据构建叙事，仍然受到我们选择框定行为、问题和解决方案的方式的影响。

所以，退后一步。想想你的假设。考虑你选择讲述的关于你的软件的故事。它们很重要。