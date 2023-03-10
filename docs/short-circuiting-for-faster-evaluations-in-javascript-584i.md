# javascript 中快速求值的捷径

> 原文：<https://dev.to/duranenmanuel/short-circuiting-for-faster-evaluations-in-javascript-584i>

在讨论短路之前，让我们快速回顾一下布尔表达式，这里我做了一个快速表格来帮助你“刷新”你的记忆:

| A | B | A <u>和</u> B | A <u>或</u> B |
| **真** | **假** | **假** | **真** |
| **假** | **真** | **假** | **真** |
| **真** | **真** | **真** | **真** |
| **假** | **假** | **假** | **假** |

记忆刷新？我知道我知道，你们大多数人可能已经知道了，但是，嘿，我不得不采取预防措施，太好了！现在我们终于准备好开始谈论真正的事情了。

让我们以下面的陈述为例:

```
 const animal = false;
const sound = 'woof';

if (sound === 'woof' && animal) {
    const dog = true;
} 
```

如果我们分析上面的表达式，我们会看到它使用了&& ( *和*)操作符，这意味着所有包含条件的语句必须*为真，表达式才能为真。*我想让你注意你评估上面条件的方式，我们先评估`sound === 'woof'`，然后评估`animal`这意味着我们评估条件 L - > R ( *从左到右*)，这是非常基本但理解起来非常重要的。

**短路** *是指语句按照 L - > R 的顺序进行求值和测试，以避免在可能的情况下对整个语句求值*，所以基本上:

`false && anything`短路是否评估为假

`true || anything`短路是否被评估为真

还不清楚？，让我根据上面的例子来启发你，但是改变条件参数:

```
const animal = false;
const sound = 'woof';

// The if statement is short-circuit evaluated to false
if (animal && sound === 'woof') {
    const dog = true;
} 
```

上面的条件将最终为假，因为`animal`为假，这意味着我们不评估`sound === 'woof'`，因为我们已经知道条件为假**，这意味着这个条件已经被短路评估为假。**没错，这就是短路的本质。

还是看不出知道这个有什么好处？根据我们所学的概念，我们可以改进我们的条件，并且总是首先评估我们的条件中最重要的参数，总是首先评估你知道最有可能定义你的语句的变量，以便它可以有更多的机会被“短路”，从现在开始记住这一点，你将对你的应用程序做出一些小而好的改进。

你喜欢这篇文章吗？在我们之前的文章[三元运算符和布尔赋值](https://enmascript.com/articles/2018/07/07/ternary-operators-and-boolean-assignations)中，我们讨论了一个主题，如果结合我们今天所学的内容，这个主题可能会非常有帮助，不要忘记与你的朋友分享它，并通过订阅我们的电子邮件列表来支持我们，下一篇再见。