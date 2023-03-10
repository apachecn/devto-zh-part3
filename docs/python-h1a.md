# 成为 Python 开发者的招聘面试准备[翻译文章]

> 原文：<https://dev.to/nabbisen/python-h1a>

本文是以下文章的翻译:
[preparing for a python developer interview](https://stackabuse.com/preparing-for-a-python-developer-interview/)by [Adam McQuistan](https://twitter.com/adamjmcquistan) on [stack abustabusk](https://stackabuse.com/)

* * *

[![cover image](img/4e45f35596507253c3a11a71c1a7eaba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b00DXmRb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stackabuse.com/conteimg/2018/09/preparing-for-python-developer-interview.png)

### 前言

我打算在这篇文章中就自己的意见和建议进行阐述。 因此，我们希望你在关于 Python 编程的面试中，能站在比其他竞争对手更引人注目的最佳位置。 如果那个能实现的话，你就能从事 Python 开发者的工作了吧。

你可能在想这种事。 现在市场上程序员不足。 所以自己要做的，只是出现在面试的场合，回答几个关于 Python 语法的问题，用自己的学历和资格说明来填补剩下的时间。 首先从这一点来说明。 实际上完全没有那样的事。 即使成功了，如果其他团队成员发现你不具备团队要求的资质，雇佣也不会很长时间吧。

那么，如果你的目标是进入 Python 的编程领域，甚至承担熟练的 Python 开发者的作用的话，我建议你就这样读下去。 在名为面试的竞技场，介绍为了尽可能接近胜利的面试秘诀。

### 加深对 Python 的理解吧

如果被录用为以 Python 开发者为对象的工作的人，对 Python 有所理解可能会被认为是理所当然的事情。 然而，也有并非如此的情况。 你也许在没有 Python 必备知识的情况下，通过虚张声势来闯过面试。 所以，如果不认真努力的话就很难了。 与其这样，不如现在就考虑一下为了掌握 Python 的最低限度的基础而花费宝贵的时间比较好吧。 这样的话，你就会明白，在得到工作的面试中，充分应对是非常困难的。 但是，如果您有多年使用其他面向对象的高级编程语言(所谓的 Java、JavaScript、C#、……等)的经验，就不在话下了。 希望你不要说这样的话来冒犯我……招聘的时候不要说谎。

作为最低限度的事情，以下的事情可以说明了吧。 关于 Python 中经常出现的语法，即循环/控制流结构/列表内涵表示。 然后也能定义基本的类吧。 如果其中包含不熟悉的东西的话，与本文(英语)位于同一个[StackAbuse](https://stackabuse.com) 上的 Scott Robinson 的[面向完全初学者的 Python 教程(英语)](https://stackabuse.com/python-tutorial-for-absolute-beginners/)

### 介绍一下项目事例吧

您在学校、编码引导营、或者现在或过去的职场上很忙吧。 在知道了这一点的基础上，但对我来说，再怎么强调这件事的重要性也是不够的。 你打算被 Python 开发职位录用。 此时，必须用有效的方法，让作为雇主的经理确信。 首先要知道你有他们需要的技能。 其次，您将为此对某个产品做出巨大的贡献，或者对某个项目做出巨大的贡献，而这个项目总有一天会成为产品并为该公司带来价值。

根据我的经验，证明编码的最好方法是出示适当分量的代码，说明有能力制造适用于软件的部件。 简单的 web APP 就可以了。 或者，它可以是用于处理数据的脚本，也可以是小型桌面 APP 应用程序。 这里重要的是，让对方觉得你有能力用正确的书写方式，并且用容易理解的代码书写构成好的东西。

实现这一点的最佳方法是拥有公共 Github、BitBucket 或 GitLab 存储库。 所以让我们公开一个样例项目(群)。 这样做会给你带来好处:

*   可以加入开源社区。 这本身就是一件很棒的事情，在那之中也会有很棒的事情吧。
*   我可以证明你也知道 Git 版本控制的基础。
*   通过你的名字出现在那里，接受类似工作联络的机会就会增加。

我先谈一下第二点。 构建样例项目时，请将其视为实际项目。 也就是说，每次完成一部分功能，在版本控制中与说明其内容的提交语句一起提交。 这件事带来的效果迟早会有令人惊讶的时候吧。 招聘中的经理，对于你了解如何管理 Git 版本并能实施，会有很大的价值。

### 磨练一下数据结构和算法相关的技术吧

首先，让我们了解一下 Python 的一般数据结构。 关于列表、字典、元组等，然后是关于班级的制作方法。

其次，了解更一般化的数据结构就好了吧。 关于连接列表、堆栈、队列等。 那些在 Python 的标准库中并不一定使用。 但是，在使用这个语言的过程中，是有可能在某个地方使用的东西。

为了能比较 Python 的基本数据结构和上述一般化的数据结构，还是比较一下比较好吧。 可以选择是使用列表那样的现有 Python 的数据结构安装堆栈那样的功能，还是手工安装[连接列表](https://stackabuse.com/python-linked-lists/)那样的类。

以下是合并列表的手动实现示例: 利用名为`Element`(虽然也起到了节点的作用)的内部类来控制数据要素。

```
class Element:  
    def __init__(self, value):
        self.value = value
        self.next = None

class LinkedList:  
    def __init__(self, head=None):
        self.head = head

    def append(self, value):
        if self.head is None:
            self.head = Element(value)
        else:
            current = self.head
            while current.next is not None:
                current = current.next
            current.next = Element(value)

    def pop(self):
        if self.head is None:
            return None

        if self.head.next is None:
            value = self.head.value
            self.head = None
            return value

        current = self.head
        while current.next.next:
            current = current.next
        value = current.next.value
        current.next = None
        return value

    def peek(self):
        if self.head is None:
            return None
        current = self.head
        while current.next:
            current = current.next
        return current.value

    def remove(self, value):
        if self.head is None:
            return None

        if self.head.next is None:
            if self.head.value == value:
                self.head = None
                return True
            return None

        if self.head.next.next is None:
            if self.head.next.value == value:
                self.head.next = None
                return True

        current = self.head
        while current.next.next:
            if current.next.value == value:
                current.next = current.next.next
                return True
            current = current.next
        return None

    def insert_first(self, value):
        next_element = self.head
        self.head = Element(value)
        self.head.next = next_element

    def delete_first(self):
        if self.head:
            new_first = self.head.next
            self.head = new_first 
```

Enter fullscreen mode Exit fullscreen mode

希望您能发现使用特定数据类型(如连接列表)很有用的情况。

例如，如果经常在列表开头添加或删除元素，则使用合并列表比使用 Python 标准列表要高效得多。 但是，这里有值得大书特书的事情。 这种操作最常见的情况是在队列或堆栈中，合并列表实际上可以应用于它们。 但是 Python 的集合模块已经具有[deque](https://docs.python.org/ja/3/library/collections.html#collections.deque) 这样的嵌入式数据结构。 因为这是很重要的事情，所以面试讨论的时候也可能会被提起。

在 Python 的面试中，当提到合并列表的手动实现这一话题时，主要是说明自己有独特的编码技能吧。 其中说的是 Python 标准列表和连接列表结构的不同。

然后，也要记住一些基本的算法。 也就是说，这是指在进行排序和检索等一般处理时使用的内容。

例如，在列表中，二叉树搜索比线性搜索表现出更好的性能。 如果能说明那是什么样的，为什么是这样的就好了。 具体来说，在线性搜索中时间计算量总是 O(n )，而在二分搜索中是 O(log n )。 对于适合使用二分搜索而不是线性搜索的情况，可能也会想说明一下吧。 如果您希望多次搜索较大的列表，那么为使列表能够进行二分搜索而付出的努力可能是值得的。 但是，在列表只进行了几次搜索的情况下，进行排序可能不会得到与其辛苦相对应的东西。 而且，有值得一提的事情。 在以下情况下，应该考虑是否只需要使用字典等其他数据结构。 即检索键可以散列化的情况。 在这种情况下，本质上可以用 O(1)执行元素的检索和添加吧。

### 理解和解决问题的力量

与记忆特定语言的语法、常用的数据结构和算法相比。 好吧，不管它们有多少价值，如果想成为华丽的开发者的话，一直都有重要的事情。 还有其他能让你看起来更突出的要素。 关于问题、用例、或者作为软件实现的好机会、甚至是被要求落到源代码中的事情所涉及的其他什么、以上内容，都是理解这一点的关键。

这里要求的是硬件和软件方面，以及两种技能的组合。 你需要积极地倾听特性上的要求和错误的详细情况，抓住相关的事实，甚至提出问题来找出额外的关键内容。 然后，您需要咬碎所有这些信息，使其落入可以执行的各个任务和组件中。 它们有机地组合在一起，不久就会发挥出所希望的功能性吧。

请相信我。 雇主想确认你的资质，最终就是这个。 你如何面对编程的任务和课题，你能否将它们用于收集关键信息并设计解决方案？

说起来容易做起来难。 但是，为了提高成功的希望，我们有可以做的事情。 总的来说，就是积累大量的实践*，挑战各种各样的课题。 致力于更多的课题，就能更多地认识课题的一般模式，不断地设计解决方案。 那些解决方案往往只需要很小的差别就可以调整。 为了积累解决编程相关课题的经验的好方法是使用[Daily Coding Problem](https://stackabu.se/daily-coding-problem) 这样的服务。*

 *Daily Coding Problem 是一种登录后，每天都能通过邮件收到 Python 表达的编程问题的服务。 举个例子，Daily Coding Problem 的主页上提供了可能会收到的问题的样本。 是这样的。 “有 n 级楼梯。 一次只能上升一级或两级。 请编写一个函数，返回在有 n 级楼梯的情况下，上楼梯有什么方法。 上行段数的顺序要区别。 ”

有趣的是，段数组合的排列可以简化为楼梯为( N - 1 )段和( N - 2 )段时的和。 你可能已经注意到，这是用于实现用于在斐波那契数列中获得第 n 个数的算法的主要逻辑。

请让我再深入一点关于这件事。

楼梯为 1 级( N = 1)时，一次只上 1 级或 2 级的上行方式有几种呢？ 只有真正的一种选择方法，即[1]。

```
N = 1 => [1] 
```

Enter fullscreen mode Exit fullscreen mode

那么在 2 级( N = 2)的楼梯上呢？

```
N = 2 => [1, 1], [2] 
```

Enter fullscreen mode Exit fullscreen mode

那么，在 n > 0 的条件下，考虑 f(N ) = f(N - 1 ) + f(N - 2 )的数学式吧。

```
[1] + ([1,1], [2]) = [1,1,1], [1,2], [2,1] 
```

Enter fullscreen mode Exit fullscreen mode

正如我刚才所说，这是斐波那契数例的递归实现。 在 Python 中，情况如下:

```
def step_combinations(stairs):  
    if stairs <= 1:
        return 1
    return step_combinations(stairs - 1) + step_combinations(stairs - 2) 
```

Enter fullscreen mode Exit fullscreen mode

Daily Coding Problem 不仅每天都会发来问题。 花一点钱，就能得到关于这些问题的[详细解说](https://stackabu.se/daily-coding-problem-subscribe)，有助于解决额外的应用题，也能把服务器发来的答案和自己的进行比较吧。

### 为了取得更好的成果

本文是关于为了从事作为 Python 开发者的工作而进行的面试。 因此，我一直将焦点放在 Python 固有的技术技能上。 但是，以我的经验来看，Python 的开发者实际上很少只写 Python 的代码。 实际上，只使用一种技术和编程语言来努力工作的想法，从想要获得长期雇佣的立场来看，是完全荒谬的。

我的建议如下。 也要注意辅助技术。 有一种技术在提问中常常被问到“知道的事情比较好”。 JavaScript、CSS、Java 等... 也调查一下那些事情吧。 这样，就表明你有能力、有意愿学习其他东西。 而且能让那家公司感受到你的价值。

再实践一件事就好了。 那就是掌握有关那家公司的知识。 对自己想要工作的公司进行基本的调查吧。 让我们关注一下那家公司的某些东西吧。 该公司拥有或打算拥有的、主要的收益来源和特征文化是什么等。

最后，我来告诉你绝对不小。 我想先谈谈面试时的服装。 为了给人留下印象，服装是不言而喻的吧。 但是，实际上有我听到或目击到的事情，开发人员中有人穿着牛仔裤或风衣出现在面试中…… 我不是兼职的！ 即使该公司的文化十分柔和，最低限度也应该穿商务便服。 但是我推荐西装。 你好不容易展示了 Python 的优秀技能，而且为了让对方佩服你对那家公司的理解而努力着。 正因为如此，让我们不要把它搞砸了。 “不，这个人可能知道编程。 但是其他 n 位候选人也是一样的。 而且，那边的人，一点也不像是从拱廊里忽悠进来的。 ”这样的印象，不能一开始就让人产生。

简而言之，*让仪容也闪耀吧。 光有 Python 的技能是不够的。*

### 在结尾

在这篇文章中，我试图表达基于自己注意到的以下内容。 也就是说，在作为 Python 开发者的录用面试中，什么会比竞争对手们更能让你引人注目呢？ 我谈到了以下事情的重要性。 而是实际了解 Python，能够使用一般的数据结构和算法，甚至通过 Daily Coding Proble 这样的服务解决很多课题，成为更优秀的课题解决者。 不仅如此，他还谈到了企业研究和适当的服装等基本事项。 希望你能在这篇文章中找到某种价值。 但我最强烈希望的是，这篇文章能帮你突破未来 Python 的面试。

再次感谢您的阅读。 如果有评论或批评的话，请联系我。

### Reference information

*   [日常编码问题](https://stackabu.se/daily-coding-problem)
*   [准备招聘程序员的面试(英文)](https://stackabuse.com/preparing-for-a-job-interview-as-a-programmer/)
*   [突破编码面试: 150 道编程问题及解答(英文)](https://stackabu.se/cracking-the-coding-interview)
*   [简明编程面试:跳槽成功的秘密(英语)](https://stackabu.se/programming-interviews-exposed-secrets-to-next-job)

* * *

谢谢您的阅读。

本記事は、以下の記事の翻訳です:
[准备接受 Python 开发者的采访](https://stackabuse.com/preparing-for-a-python-developer-interview/)由[亚当·麦克基斯坦](https://twitter.com/adamjmcquistan)讲述[栈滥用](https://stackabuse.com/)

致亚当:非常感谢你允许我翻译你的文章。*