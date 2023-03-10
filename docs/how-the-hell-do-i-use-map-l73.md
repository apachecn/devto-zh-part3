# 我到底怎么用地图？

> 原文：<https://dev.to/liltechnomancer/how-the-hell-do-i-use-map-l73>

这是我的个人博客上的一篇文章，位置是[这里](https://lvrbrtsn.com/blog/how-the-hell-do-i-use-map/)T2 】,它们提前一天左右发表在那里。多漂亮啊。

我经常鼓励我的学生使用数组函数`map`、`reduce`和`filter`，但是他们很少对它们感到舒服，这真的很遗憾，因为它们就像一把装有手电筒的瑞士军刀。这三个函数可能会删除成千上万行最令人困惑的错误代码。让我们从这群人的全明星`map`开始。

地图是一种有趣的功能，它起初看起来非常奇怪，就像有人剥掉了你所知道的 JavaScript，喜欢展示一些古老的外星技术。

```
array.map(x => x * 5) // What r u even Doing tho!!!! 
```

Enter fullscreen mode Exit fullscreen mode

让我们分解这一行代码来获得一个基线。

首先，我想指出箭头函数`x => x * 5`(如果你不熟悉什么是箭头函数，请阅读这篇[博文！](https://lvrbrtsn.com/blog/wtf-are-arrow-functions)我希望能很好地解释它们。)

一个函数在那里做什么？这是回调吗？良好的...有几分地...但是不，如果你愿意的话，可以把它看作一个回调函数，因为它是一个传递的**函数**和一个* *参数* *，另一个函数决定它的执行...所以从几个方面来说，这是*像*一个回调。尽管如此，这不是一个回调，相反，它被称为* *高阶函数* *，这是另一天的博客帖子。

好，好，所以`map`把一个函数作为参数...但是为什么呢？它和它有什么关系？

嗯，map just **在你的数组上迭代**，这意味着它一个接一个地获取数组中的每一项，并将其传递给你提供的函数。

所以如果你看到这个

```
const scores = [90, 80, 25, 50]

const doubleScores = sores.map(x => x * 2) 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以确定通过计算每一项的分数并通过`x => x * 2`来计算`doubleScores`。那我们会剩下什么呢？

它必须是一个数组，因为我们需要存储 4 个结果。所以我们知道`doubleScores`将会是一个数组。它是一个数组，填充了一些其他数组内容加倍的结果。

```
// A way to visualize what map is doing
cosnt scores = [90, 80, 25, 50]
const double = x => x * 2
const doubleScores = [ double(scores[0]),
                       double(scores[1]),
                       double(scores[2]),
                       double(scores[3]) ] 
```

Enter fullscreen mode Exit fullscreen mode

好吧，那么地图*做什么*？

好吧，解释它的一种方式是，它允许你接受一个期望**单个项目**作为参数的函数，就像上面的例子，一个单一的数字。并将此函数用于包含许多项目的数组。惊叹声...这就像一个口袋`for loop`,每个阵列都可以随身携带。

这实际上很棒，因为这意味着我不用写循环，我可以写简单的函数，用来操作单个项目，然后用它们来转换项目列表。这真的很棒，因为`for loops`有 cruft 和样板文件可以掩盖代码的真正意图*。考虑下面的代码。* 

```
const students = [ { name: 'Leroy Jenkins' grade: 55 },
                   { name: 'Tommy Pickles' grade: 98 },
                   { name: 'Mike Tyson' grade: 85 },
                   { name: 'Donnie Darko' grade: 90 } ]

// A simple function expecting a single student, and returns true if they pass the class and fail otherwise.
const isPassing = (student) => student.grade >= 65 ? true : false

// Usage with a single student
const tommyPickles = students[1]
const didTommyPass = isPassing(tommyPickles)

// Usage with the entire list
const classResults = students.map(isPassing)

// Extra credit c-c-combo usage with filter
const onlyWinners = students.filter(isPassing) // more on this next week. 
```

Enter fullscreen mode Exit fullscreen mode

多棒啊。你能想象在 for 循环中使用这个函数吗？它看起来会很乱，相反，它看起来像一个漂亮的单行声明。地图给了我们超能力！
当我说它像一个口袋`for loop`时，我是认真的，实际上任何`for loop`都可以用 map 重写，而且通常结果会简单得多。例如，上面的第一个 For 循环看起来像这样

```
const scores = [90, 80, 25, 50]
const doubleScores = []
for (var i = 0; i < scores.length; i++) {
  doubleScores.push(scores[i] * 2)
} 
```

Enter fullscreen mode Exit fullscreen mode

Eww wtf 号带回地图版本

`const doubleScores = sores.map(x => x * 2)`

ohhhh 是的...好多了。

看出区别了吗？地图版适合一条线，少了很多粗鄙，让你看起来更酷...好吧，也许最后那部分不是真的。更重要的一点是，如果你知道如何使用`map`，就很少需要编写循环。实际上，我已经很久没有写 for 循环了，所以当我写上面这个循环的时候，我不得不仔细检查 MDN。老实说，我已经不怎么用它们了。*