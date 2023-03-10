# 条件(三元)运算符

> 原文：<https://dev.to/samy80/the-conditional-ternary-operator-546o>

# 打破僵局

对于我们这些从未听说过它的人来说，关于*条件(三元)运算符*，或条件运算符，或三元运算符，甚至 c24r，有一个很长的名字是很清楚的。但是它到底是什么，如何以及何时使用它呢？

操作符是一种紧凑的语法，旨在简化条件代码(if-then-esle)，在许多编程语言中都可以使用。但是像所有强大的东西一样，必须负责任地使用它，冒着失去其简化目的的风险。

顾名思义，操作符有三个参数:一个条件和两个结果，第一个条件为真，另一个为假，用于赋值。理论够了，给我点代码！

# 什么时候用？

让我们来回顾一下下面的代码:

```
<?php
if ($a > $b) {
  echo "a is greater than b";
} 
else {
  echo "a is lower than b";
}
?> 
```

很简单。首先，让我们重塑它，以隔离逻辑组件:

```
<?php
$isGreater = $a > $b;
if ($isGreater) {
  $word = 'greater';
} else {
  $word = 'lower';
}
echo "a is $word than b";
?> 
```

这段代码是条件运算符的典型候选:依赖于条件的赋值。我们来重构:

```
<?php
$isGreater = $a > $b;
$word = $isGreater ? 'greater' : 'lower';
echo "a is $word than b";
?> 
```

已经很好了，但我们可以做得更好:

```
<?php
$word = $a > $b ? 'greater' : 'lower';
echo "a is $word than b";
?> 
```

我们获得了一行，但是以显式条件语义为代价。在这里，我们已经可以感受到权衡:紧凑与可读。让我们继续前进。

# 什么时候不用？

更进一步是诱人的。我们可以进一步简化，得到一个很棒的 liner:

```
<?php
echo 'a is '. ($a > $b ? 'greater' : 'lower') . ' than b';
?> 
```

但这就是我们需要停止的地方。这里丢失了太多东西，我们正在破坏简化的最初目的。事实上，即使代码看起来更简单，因为所有东西都在一行上，它变得不那么清晰/可读，人们必须仔细阅读几次才能理解它的意思。

# 总结起来

我们已经看到了条件操作符是如何简化代码的，但是当它被滥用时也会降低可读性。代码并不意味着是紧凑的，那是编译器的责任，而是可读的，作为人类的目标，因此更容易维护。

那么，一班轮公司真的要被禁止吗？不。正确的平衡是将它们的使用限制在赋值上，有时也限制在后缀上:

```
<?php
//assignments
$word = $a > $b ? 'greater' : 'lower';
$colspan = $row > 0 ? 5 : 2;
$backgroundColor = $row % 2 == 0 ? '#E3E3E3': 'none';

//postfixes
$formattedValue = $value . ($isPercent ? '%' : '');
$amount = $value . ($isEuro ? '€' : '$');
?> 
```

你呢？你是一个*条件(三元)运算符*上瘾者吗？你刚刚发现它吗？你有特殊的魔术代码技巧吗？这个帖子让你生气了吗？请在评论中分享你的经历和感受！