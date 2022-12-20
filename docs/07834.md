# 什么是 else 语句？

> 原文：<https://dev.to/codetips/what-is-an-else-statement-39ac>

> else 语句是当条件**不**满足时运行的代码块

[![What is an else statement?](img/96b8bc4aa0991ecb034b82fc1c5ebe9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EQT1ozY7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1516431883659-655d41c09bf9%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

在[中，什么是 if 语句？](https://codetips.co.uk/beginner/what-is-an-if-statement/)向您介绍了`if`项陈述。我们提出了一个问题("*)我应该出去走走吗？*")并写了一些[伪代码](https://codetips.co.uk/beginner/what-is-pseudo-code/)，说明如何在 *`if`* 语句中表示这一点:

```
if (takeWalkOutside) {
    // put on your shoes
} 
```

但是，如果我们的逻辑不是这么简单，而是想根据天气找出我们应该穿什么鞋呢？

幸运的是，我们只有两双鞋，所以我们的逻辑仍然很简单:如果外面下雨，我们想穿雨靴，否则我们会穿运动鞋。

这是我们可以使用`else`语句的地方。

```
if (raining) {
    // put on wellies
} else {
    // put on trainers
} 
```

在这个例子中，每当`raining`条件被**而不是**满足时，我们将执行动作(穿上运动鞋)。

但是如果我们不仅仅拥有两双鞋呢？如果我们有凉鞋，在晴天穿，有雪地靴，在下雪的时候穿。

使用`else`语句不会帮助我们实现这一点。目前，我们只是检查是否在下雨，其他情况都会导致相同的行动(穿上运动鞋)。

然而，我们可以使用`else-if`。这将允许我们检查多个条件。

```
if (raining) {
    // put on wellies
} else-if (snowing) {
    // put on snow boots
} else-if (sunny) {
    // put on sandals
} else {
    // stay indoors
} 
```

现在我们可以检查是下雨、下雪还是晴天，并穿上合适的鞋子。

注意我们仍然有`else`语句，但是这次如果是其他类型的天气，我们将呆在室内。