# 另一个三元组

> 原文：<https://dev.to/samholmes/an-alternative-to-ternary-5cei>

像许多语言一样，JavaScript 有条件(三元)操作符。这个操作符的奇妙之处在于它可以嵌套在表达式中，并且返回值。这对函数式编程和可组合性来说是一件好事。

然而，有时你不需要条件的第二个“假”部分，所以你最终得到了一个假条件的占位符。这在 React/JSX 中体现得相当频繁:

```
return (<div>
  {confirm ? (
    <p>Your action has been done</p>
  ) : (
    ''
  )}
</div>) 
```

这就像强迫程序员每次使用`if`都要使用`else`。

# 另类

三元运算的替代方法是使用`&&` (AND)运算。

```
return (<div>
  {confirm && (
    <p>Your action has been done</p>
  )}
</div>) 
```

因为如果左操作数为 falsey，AND 运算符将短路，所以它的作用与三元运算符的第一部分相同。

更有趣的是，我们可以将 AND 运算符与`||` (OR)结合起来，得到与三元运算符一样的效果:

```
return (<div>
  {confirm && (
    <p>Your action has been done</p>
  ) || (
    ''
  )}
</div>) 
```

这意味着我们可以很容易地将一个有条件关注的语句扩展到两个关注。

我们不会就此止步。我们可以像嵌入三元组一样嵌入这些 AND/OR 语句。

```
return (<div>
  {isHappy && (
    <p>I'm feeling good</p>
  ) || (
    isHungry && (
      <p>I'm feeling hangry</p>
    ) || (
      <p>I'm feeling sad</p>
    )
  )}
</div>) 
```

可以看到，`&&`和`||`分别映射到`if`和`else` pretty。精彩！

# 明白了

如果您决定使用这种模式，需要注意一些事情。

1.  括号很重要。规则是始终包装你的结果表达式:`condition && (outcome) || (outcome)`。这样做将允许您在结果表达式中清晰地嵌套更多的条件表达式。请注意，如果您不打算嵌套更多的条件表达式，则没有必要这样做。
2.  表达式计算的结果可能不是预期的。例如:`var selectedItem = selectedItemId && items[selectedItemId]`在这种情况下，你可能会认为`selectedItem === undefined`在`selectedItemId`为假的情况下。不正确。毋宁说，`selectedItem === selectedItemId`。这是因为一个`&&`运算的结果要么是最后一个真值，要么是第一个假值(本例就是这种情况)。这种不正确的期望应该伴随着错误值的明确结果:`var selectedItem = selectedItemId && items[selectedItemId] || undefined`

# 命名图案

这种模式需要一个好名字，以便团队可以决定是否采用这种模式。让我们来看看:

> 短路条件(SCC)