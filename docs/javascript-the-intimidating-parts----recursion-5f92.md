# Javascript:令人生畏的部分——递归

> 原文：<https://dev.to/brandonsheehy_/javascript-the-intimidating-parts----recursion-5f92>

[这篇文章最初出现在我的个人博客](https://brandon.irish/jstip-recursion/)

如果你像我一样是一个自学成才的程序员，你很有可能害怕递归的想法。我不是计算机科学专业的，所以我总是像躲避瘟疫一样避开递归。我知道它与重复某事有关，也许它涉及一个调用自身的函数？但是你会怎么做呢？为什么你会这么做？！

简单来说，**递归就是一个函数一遍又一遍的调用自己，直到达到想要的结果。这个定义可能会也可能不会让你明白太多。让我们来看看帮助我点击概念的例子。别担心，这不是你所熟悉的旧阶乘例子。**

我第一次真正理解递归，其实是偶然发现的。这恰好是我能想到的解决问题的最好方法。我所做的事情的基本前提是从数据库中提取主键，并将它们转换成代码生成器的变量声明。因此，如果数据库将`client xyz id`作为`2483954`返回，我需要输出`const CLIENT_XYZ_ID = 2483954`。很简单，对吧？

当然，变量名中不能有空格，所以我需要用下划线代替值中的空格。那很简单。我可以有一个类似于
的函数

```
function formatVariableName(databaseValue) {
  return databaseValue.toUpperCase().replace(/ /g, '_')
} 
```

(如果你不熟悉 RegEx，这基本上只是说用下划线替换字符串中的每个空格。)

然而，我遇到了一个问题。数据库字段由客户经理填写，他们对我薄弱的正则表达式技能毫不关心。因此，它们通常在一行中有多个空格，但是我不希望我的脚本在一行中输出带有多个下划线的变量名。那现在怎么办？

嗯，我可以做类似
的事情

```
function removeDuplicateUnderscores(variableName) {
  return variableName.replace(/__/g, '_')
} 
```

我们用一个下划线替换两个下划线的实例。

如果一行中只有两个下划线，那就太棒了*。如果我这样假设，我会是什么样的程序员呢？然而，如果我们知道一行中只有两个下划线，知道这个函数可以工作，这恰好是一个非常重要的事实，以便在这里编写一个好的递归解决方案。那是因为**递归函数有基本情况。**基础案例只是一个简单的测试，用于确定该功能是否需要再次运行，或者其工作是否已经完成。*

那么，如果我们让函数一直运行，直到一行中没有超过 1 个下划线的实例，会怎么样呢？(这个代码片段将使用[regexp . prototype . test](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test))

```
function removeDuplicateUnderscores(variableName) {
  if (!/__/.test(variableName)) return variableName
  return removeDuplicateUnderscores(variableName.replace(/__/g, '_'))
} 
```

让我们来看一下这是怎么回事...

首先是基本情况。我们如何知道这个函数何时完成了它的工作？当一行中不再有两个下划线时。所以这一行只是测试两个连续下划线的存在。如果没有，我们就完了！返回该值。

下一位是实际的递归。我们传递给函数的值是 variableName，但是每组两个连续的下划线被替换为一个下划线。就像我们之前做的一样。但是该值随后又被传递到`removeDuplicateUnderscores`中，以防那里还有`__`的情况。这将重复进行，直到我们达到我们想要的结果，也就是我们的基本情况。

我希望这个例子对你和对我一样有帮助。即使它只有一点点帮助，我希望它向您展示递归真的不是什么疯狂的事情。我鼓励您打开浏览器控制台，体验一下这些功能。测试它们，看你是否能打破它们，看你是否能改进它们。

## 交底

如果你想知道是否有更好的方法来做到这一点，有。这可以通过改进我的正则表达式来完成，而不是使用递归。幸运的是，这对这个例子的可教性没有影响。