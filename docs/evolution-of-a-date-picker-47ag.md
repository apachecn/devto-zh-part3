# 日期选择器的演变

> 原文：<https://dev.to/nuclear0/evolution-of-a-date-picker-47ag>

今天我想和你分享一个关于日期选择器(日历，日期输入字段)在我们公司是如何发展的故事。

日期输入在我们的项目中是一件非常重要的事情，但就 UX 而言，这项任务并不容易。

**第一版**

[![first](img/b770429a6b0fdb675d7557d9d6c3736e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D4YRalZL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3i9fmpk0swwwrg3ecw3y.png)

问题:

1.  我们复制了日期，让它可以用键盘更改。对我来说，这样的双重信息非常令人困惑，而且占据了大量的屏幕空间。

2.  你需要确认日期的输入。这需要额外的点击，并有可能不保存日期。在某些情况下，用户确认是有帮助的，但不是在这种小事上。

**第二版**

[![second](img/0199fadb7ae2d3bd2e61a48839eb881d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WdSxvcmz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iznow9tmqbw82homsn7r.png)

正如你从截图中看到的，这两个问题都解决了，我们有了一个更常见的日期选择器。

但是有一个问题困扰了我好几天。

它是月份和年份的选择。在这个变体中，你可以从键盘输入，但它不是很快(如果你应该从键盘输入一些东西，那么我们可能根本不需要日历)，或者你可以滚动月份，但我不认为滚动数百个月到生日是一种真正的乐趣。

你可以在网上找到的一些变体。

[![bootstrap](img/dadb46e40aa78e113c4bc6cc0bfaefcd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cDrk5a4n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6iscxhyanz1y82yrvpqa.png)

第一个变体——点击年份，您将看到年份表。在某些实现中，您应该首先选择十年，然后选择年份本身。几个月都是同一张桌子。

这是对我最有吸引力的变体，它看起来非常直观。但它从未通过走廊测试。主要的争论是——点击次数太多，比你自己输入日期要慢。

[![react-datepicker](img/d92b3c57f19feacde7c26bda0e9a3352.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x7iSISjh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bm2wrjwpaedd78oetg1r.png)

另一个循环变量—用下拉列表选择年和月。

它有一个主要缺点——看起来很糟糕。现在你有一个带有日历的弹出窗口，下拉列表是年份…一般来说，穿过弹出窗口不是一个合适的语气。

**当前版本**

[![current](img/684184c2564a3c9fc7d75188fd7dd495.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YvQpyPte--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aa3xvf7vhtyr6xny45wy.png)

在当前版本中，您可以从日期旁边的滚动列表中选择月份和年份。所有问题都解决了，大家都满意了。

我应该提到两个非常有用的技巧:

1.  当窗口打开时，当前月份和年份应该在窗口的中央。用户不应该感到困惑，应该知道选择了什么日期。

2.  列表应该指出您可以滚动的内容。在屏幕截图中，您可以看到列表顶部的颜色变浅，但底部没有。

我认为这不是最终版本，但现在对我们来说是最有用的。