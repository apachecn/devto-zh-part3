# 关于“干净代码”的注释第 2 章:“有意义的名称”

> 原文：<https://dev.to/tangweejieleslie/chapter-2-meaningful-names-1cgj>

本章提供了良好命名实践的列表和示例。通读这一章会让你感觉到什么是好的，什么是坏的，但是尝试一下子实现所有这些是有点令人畏惧的。

## 好的做法要纳入

*   **使用透露意图的名字**

使用的名称应该是不言自明的

*   **使用名词或名词短语作为类名**

好的类名的例子:`Customer, WikiPage, Account, Address`是好的类名。

错误的类名的例子:`Manager, Processor, Emitter`是错误的类名，因为它们暗示了某种动作。

*   **为方法名加入动词或动词短语**

好的函数名的例子:`postPayment(), deletePage(), save()`。

访问器、赋值器和谓词应该以 get、set 和 is 为前缀，并以它们的值命名。

比如`getTimeStamp(), setDateOfBirth(), isUserAvailable`。

*   **使用可发音的名字**

不要创造自己的首字母缩写词或单词来描述可以用普通单词交流的东西

*   **避免虚假信息**

我觉得我的一些命名可能会导致错误的结论。给出的一个例子是“accountList ”,它可能意味着底层数据结构是一个列表。另一个例子是使用可能有其他意思的缩写。最后，我似乎一直犯的一个错误是对一个相似概念的不一致的命名。

*   **为每个概念选择一个词**

这与虚假信息有很好的联系。例如，为同一个概念选择许多单词可能会导致错误信息，例如 get vs fetch vs retrieve。

*   **使用可搜索的名称**

尤其是在多个地方使用该名称的情况下。书中的一段引文:

> *“名称的长度应与其作用域的大小相对应”*

*   **添加有意义的上下文**

例如，使用`state`作为变量的函数可能不会让您推断出太多，但是使用`state, firstName, lastName, city, address, country`的函数可以清楚地表明它形成了一个地址。

*   **使用解决方案域名**

既然你的代码的读者都是程序员，那就继续使用计算机科学术语，比如算法名、模式名、数学术语等等。

* * *

## 避免不良做法

*   **避免心理映射**

这涉及到名称的清晰性。例如，我们不希望读者在心里把提供的名字翻译成他们已经知道的其他名字。这通常是因为既没有使用**问题领域**也没有使用**解决方案领域**术语。

*   **不要添加不必要的上下文**

例如，在每个类前面加上应用程序的名称，或者在每个变量前面加上类名。

*   **以清晰度为代价融入娱乐价值**

比如用`whack()`来表示`kill()`。从书中引用的经验法则:

> *“说你的意思。说话算数。”*

## 三份外卖

在看完本章的各种例子后，我个人的收获是:

1.  保持名字简洁明了
    如果我们能牢记保持名字简洁明了，我们很可能会领先几步。使用名词和动词是实现这一点的一些方法。

2.  保持事情的一致性我想适当的文档会有所帮助，特别是对于那些涉及许多参与者并且跨越很长时间的项目。让每个人都遵循标准可能会消除误解和误解。

3.  把读者放在心上
    正如作者写文章时把读者放在心上一样，我们也应该把下一个程序员放在心上。实现这一点的简单经验法则是明确、清晰，并且不要假设下一个人知道你对代码所做的一切。

我认为意识到我正在犯的错误是纠正它们的第一步。接下来就是保持这种意识，慢慢养成习惯。