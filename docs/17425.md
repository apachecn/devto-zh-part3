# Koping 与 KDB 002:更多的名单逆转

> 原文：<https://dev.to/alifaizankazmi/koping-with-kdb-002-more-list-reversal-3kd5>

##### 预计阅读时间:10 分钟

在之前的[帖子](https://dev.to/alifaizankazmi/koping-with-kdb-001-list-reversal-3079)中，我们没有应用递归来反转列表，因为我们发现了一种更奇怪的方法。让我们看看递归的使用是否会导致更简单的代码。

## [T3 给我你的`ifs`，你的`elses`...](#give-me-your-raw-ifs-endraw-your-raw-elses-endraw-)

是的，使用条件求值来控制 Q 脚本的执行是可能的，尽管不鼓励这样做:只要有可能，重构代码以避免条件求值是首选方法。

我们可以使用的是`if`语句:

```
n: 1
if[n=1;message: "I don't think this is useful to us"]

message
-> "I don't think this is useful to us" 
```

if 语句评估一个给定的条件(`n=1`)，如果条件评估的值大于 0(表示布尔值`false`)，那么该条件后面的任何语句都按从左到右的顺序执行。在我们的例子中，我们将字符串`"I don't think this is useful to us"`赋给变量`message`。

* * *

顺便说一下，注意作用域的概念在这里并不适用:变量`message`被定义在`if`语句内部，但是在语句外部仍然可以访问！

* * *

是的，这不符合我们的用例:如果我们要使用递归，我们还需要一个`else`子句——如下所示:

```
if base case reached
    return
else
    continue list reversal 
```

## ...或者给我你的 ternaries

q 支持 Java、JavaScript 和 C#等语言中可用的三元运算符`?`:

```
$[1b;"Execute if true";"Execute if false"]
-> "Execute if true"

$[0b;"Execute if true";"Execute if false"]
-> "Execute if false" 
```

给`$`操作符(`1b`或`0b`)的第一个表达式是要计算的条件。如果条件评估为真，则执行第二个表达式，否则执行第三个表达式。如果您想对一个条件执行多个表达式，那么您可以将表达式封装在方括号中:

```
$[1b;[a:2;b:3];c:4]
a
-> 2
b
-> 3 
```

我想我们知道的足够多了，可以开始颠倒我们的列表了。前进到递归！

## 一个简单的(r)例子

在我们将递归应用于我们的问题之前，我们可以尝试在一个简单的问题上测试它，以确保它如预期的那样工作。我们可以定义一个递归函数，它不断地给一个数加 1，直到这个数等于 6:

```
recur: {[number] $[number=6;"Done";recur[1 + number]]} 
```

看起来有用！

```
recur 1
-> "Done" 
```

但是，我们能确定递归正在发生吗？因为我不知道 Q 中的`println`的等价形式，所以确保递归确实发生的一种方法是向函数传递一个大于 6 的数。在像 Java 这样的语言中，这会产生一个堆栈溢出，因为递归函数会一直调用自己，直到堆栈上没有空间了。这也正是这里发生的事情:

```
recur 7
-> `stack
-> @
-> {[number] $[number=6;"Done";recur[1 + number]]}
-> 2008 
```

不知道`@`和`2008`是什么意思。稍后我可能会回到 Q 如何显示异常(Q 异常更喜欢被称为“信号”)。

## 最后，递归

不等等。在构造函数之前，我们还需要一个操作:用于连接两个列表的`join`操作符。运算符只是一个逗号:

```
1 2 3,4 5 6
-> 1 2 3 4 5 6

1,2 3 4
-> 1 2 3 4 
```

抱歉，还有一件事:我们还需要一种方法来减少每次递归调用的列表大小，直到我们只剩下一个元素的列表。输入`cut` ( `_`)运算符:

```
list: 1 2 3

1 _ list
-> 2 3

-1 _ list
-> 1 2 
```

最后，这是我们的函数:

```
reverseList: {[list] 
    $[1 = count list;
        list;
        reverseList[1 _ list],-1 _ list
    ]
}

reverseList 1
-> 1
reverseList 1 2
-> 2 1
reverseList 1 2 3
-> 3 2 1 2 
```

啊哦，最后的结果显然是错的。为什么？让我们做一次预演:

```
reverseList 1 2 3 -> call reverseList[2 3]
reverseList 2 3 -> call reverseList 3
reverseList 3 -> return 3
reverseList 2 3 -> return 3 joined with 2
reverseList 1 2 3 -> return 3 2 joined with 1 2 
```

这就是错误所在:如果我制定了一个合适的算法，事情会变得更加明显:

```
Given a list L
Its reverse is a reverse of the list t(L) 
    (where t denotes the tail - e.g., t(1 2 3) is 2 3) 
Joined with h(L) 
    (where h denotes the head - e.g., h(1 2 3) is 1)
The reverse of a list with one element is the list itself 
```

简而言之，我们传递给`$`操作符的最后一个表达式是错误的。我们应该使用`take` ( `#`)操作符:

```
list: 1 2 3
1#list
-> ,1 
```

`1`前的逗号只是一种表示`1`不是数字的方式:它是一个单项式列表。

这里什么都没有:

```
reverseList: {[list] 
    $[1 = count list;
        list;
        reverseList[1 _ list],1#list
    ]
}

reverseList 1
-> 1
reverseList 1 2
-> 2 1
reverseList 1 2 3
-> 3 2 1
reverseList "A string is a list of characters"
-> "sretcarahc fo tsil a si gnirts A"
reverseList "Sigh of relief"
-> "feiler fo hgiS" 
```

## 绕道:阿 q 的`reverse`

我前面提到过，Q 已经有了一个`reverse`函数。它看起来像什么？我们可以在 Q 提示符下键入函数名来找出答案:

```
reverse
-> |: 
```

`|:`算一个运营商吗？是两个吗？为什么函数体里没有参数？`|:`是在列表之前还是之后？这么多问题。我们可以回答最后一个问题:

```
|: 1 2 3
-> `

1 2 3 |:
-> 3 2 1 
```

既然`|: 1 2 3`返回一个信号，看来正确的使用方式`|:`肯定是第二种。粗略地看一下各种 Q 操作符，并不能得出任何关于如何解释`|:`的答案。也许它是来自`k`的一个操作符(或者两个操作符)，一种 Q 所基于的语言。我很乐意将此事推迟到以后(如果你这样做，我很抱歉！).

## 尾部递归

我们的递归函数不是尾递归的。我们试着换一个吧。我们需要某种累加器来保持反向子列表的运行状态。一旦我们得到一个包含一个元素的列表，我们将简单地把这个元素和反转的子列表连接起来，并使它成为列表的新的头部。以下是演示我的意思的预演:

```
reverseList 1 2 3 -> call reverseList[2 3] with accumulator set to 1
reverseList 2 3 -> call reverseList 3 with accumulator set to 2 1
reverseList 3 -> end of list, return 3 joined with accumulator 
```

这是我们的尾部递归函数:

```
reverseList: {[list;acc] 
    $[1 = count list;
        list,acc;
        reverseList[1 _ list;(1#list),acc]
    ]
} 
```

尾部递归使得函数有点难以理解，但是在堆栈上却很容易理解。

```
reverseList[1 2 3;()]
-> 3 2 1
reverseList[`a`b`c`d;()]
-> `d`c`b`a 
```

注意我们需要传递的难看的`()`作为累加器的初始值- `()`表示一个空列表。我们可以通过在内部函数中隐藏`()`来清理函数:

```
reverseList: {[list] reverseListInner[list;()]}
reverseListInner: {[list;acc] 
    $[1 = count list;
        list,acc;
        reverseListInner[1 _ list;(1#list),acc]
    ]
} 
```

我们的解决方案显然不如 Q 的简洁，但是我们确实学到了`$`、`_`和`#`操作符。

```
reverseList 1 2 3
-> 3 2 1
reverseList 1
-> ,1 
```

呃，看起来我们的函数在把一个条目作为参数传递时，返回了一个条目列表。不是我们真正想要的。我们可以在外部函数中处理这种情况:

```
reverseList: {[list] 
    $[1 = count list;
        list;
        reverseListInner[list;()]
    ]
}

reverseList 1
-> 1
reverseList 1 2 3
-> 3 2 1 
```

总而言之，我们的解决方案如下:

```
reverseList: {[list] 
    $[1 = count list;
        list;
        reverseListInner[list;()]
    ]
}
reverseListInner: {[list;acc] 
    $[1 = count list;
        list,acc;
        reverseListInner[1 _ list;(1#list),acc]
    ]
} 
```

出于不同的目的在两个地方做同样的检查(`1 = count list`)很难看，不过我就讲到这里吧。