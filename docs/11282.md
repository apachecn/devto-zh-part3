# 死简单 Python:列表理解和生成器表达式

> 原文：<https://dev.to/codemouse92/dead-simple-python-list-comprehensions-and-generator-expressions-2mb5>

喜欢那些文章吗？买这本书！ [***死简单蟒*** 作者杰森·c·麦克唐纳可从无淀粉出版社购得。](https://nostarch.com/dead-simple-python)

* * *

如果我不得不选择 Python 最喜欢的特性，那肯定是 **list comprehensions，** hands down。在我看来，它们概括了“Pythonic”代码的本质...这很讽刺，因为它们实际上是从 Haskell 那里借来的。

我很兴奋能见到他们；这是我几周前计划写的文章，但是我意识到理解迭代器对于真正理解列表及其潜力是必不可少的。

如果你还没有读过前两篇关于[循环和迭代器](https://dev.to/codemouse92/dead-simple-python-loops-and-iterators-15bp)和[迭代器强大工具](https://dev.to/codemouse92/dead-simple-python-iteration-power-tools-3i3n)的文章，你会想现在就回去看看。

# 列表理解与生成器表达式

**生成器表达式**是一种生成容器的简洁方法。最常见的是，你会听说**列表理解**，但是**集合理解**和**字典理解**也存在。然而，术语上的差异有些重要:如果你真的在列清单，这只是一个清单理解。

一个**生成器表达式**被括号`( )`包围，而一个**列表理解**被方括号`[ ]`包围。集合理解**用花括号`{ }`括起来。除了这些差异，这三种情况下的语法都是相同的！**

(对字典的理解还有更多，我们稍后会谈到。)

生成器表达式与**生成器**有着千丝万缕的联系，我们将在后面的章节中深入探讨。现在，我们将满足于[对生成器表达式](https://docs.python.org/3/glossary.html#term-generator-expression)的官方定义:

> 生成器表达式-返回迭代器的表达式。

# 构造一个生成器表达式

一个生成器表达式(或列表/集合理解)有点像一个翻转过来的`for`循环。

举个简单的例子，让我们回忆一下上一篇文章中的一个例子，我们将华氏温度转换为摄氏温度。我将稍微调整一下，这样数字将存储在另一个列表中，而不是直接打印出来。

```
temps_f = [67.0, 72.5, 71.3, 78.4, 62.1, 80.6]
temps_c = []

def f_to_c(temp):
    return round((temp - 32) / 1.8, 1)

for c in map(f_to_c, temps_f):
    temps_c.append(c)

print(temps_c) 
```

Enter fullscreen mode Exit fullscreen mode

信不信由你，列表理解将允许我把整个程序减少到三行！我们一次简化一部分，这样你就能明白我的意思了。

让我们从用列表理解代替 for 循环开始...

```
 temps_f = [67.0, 72.5, 71.3, 78.4, 62.1, 80.6]

def f_to_c(temp):
    return round((temp - 32) / 1.8, 1)

temps_c = [f_to_c(temp) for temp in temps_f]

print(temps_c) 
```

Enter fullscreen mode Exit fullscreen mode

重要的台词是`temps_c = [f_to_c(temp) for temp in temps_f]`。这与`map()`的行为非常相似。对于列表`temps_f`中的每个元素`temp`，我们应用函数`f_to_c()`。

现在，如果我在别的地方需要那个`f_to_c()`函数，我会在这里停下来，称它为 good。然而，如果这是我唯一需要华氏温度到摄氏温度的逻辑的地方，我可以完全避开这个函数，直接把逻辑移到理解中:

```
temps_f = [67.0, 72.5, 71.3, 78.4, 62.1, 80.6]
temps_c = [round((temp-32) / 1.8, 1) for temp in temps_f]
print(temps_c) 
```

Enter fullscreen mode Exit fullscreen mode

我告诉过你什么？三条线！

根据我从哪里得到的数据，我甚至可以进一步减少。让我们看另一个例子。

假设您有一个程序，它在一行上接收一串整数，用空格分隔，比如`5 4 1 9 5 7 5`。你想找出所有这些整数的和。(为了简单起见，假设您没有错误输入的风险。)

让我们以最简单的方式开始写吧，*不需要*一系列的理解。

```
user_input = input()
values = user_input.split(' ')

total = 0

for v in values:
    n = int(v)
    total += n

print(total) 
```

Enter fullscreen mode Exit fullscreen mode

很明显，对吧？我们将用户输入作为一个字符串，然后在空格上分割该字符串以获得单独的数字。我们创建一个变量来存储我们的总数，然后使用一个循环来迭代每个值，将它转换为一个整数，并将其添加到总数中。现在我们有了工作逻辑，让我们简化和优化它。

让我们从简化几个显而易见的事情开始。我们以前讨论过所有这些概念，所以看看你是否能发现我改进了什么。

```
values = input().split(' ')
total = 0

for v in values:
    total += int(v)

print(total) 
```

Enter fullscreen mode Exit fullscreen mode

我们不能比这更简单了，除非我们使用列表理解，所以让我们现在就做吧！

```
values = input().split(' ')
total = sum(int(v) for v in values)
print(total) 
```

Enter fullscreen mode Exit fullscreen mode

这里的生成器表达式是`(int(v) for v in values)`。对于列表`values`中的每个值`v`，我们将其转换为一个整数(`int(v)`)。

注意我是如何使用`sum()`函数的，将生成器表达式直接传递给它。因为表达式是作为唯一的参数直接传递的，所以不需要额外的一对括号。

现在，如果我不需要`values`列表来做其他事情，我可以把这个逻辑直接移到生成器表达式中！

```
total = sum(int(v) for v in input().split(' '))
print(total) 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？

## 嵌套列表理解

相反，如果我们想要输入的每个数字的平方和呢？事实上，有两种方法可以做到这一点。显而易见的选择是这样做:

```
total = sum(int(v)**int(v) for v in input().split(' '))
print(total) 
```

Enter fullscreen mode Exit fullscreen mode

这很有效，但不知何故就是感觉不对，不是吗？我们将`v`转换成整数*两次*。

我们可以通过在生成器表达式中嵌套列表理解来解决这个问题。

```
total = sum(n**2 for n in [int(v) for v in input().split(' ')]) 
```

Enter fullscreen mode Exit fullscreen mode

列表综合和生成器表达式被评估为*内部*到*外部*。最里面的表达式`int(v) for v in input().split(' ')`首先运行，方括号`[ ]`将它转换成一个列表(一个 iterable)。

接下来，运行外部表达式`n**2 for n in [LIST]`，其中`[LIST]`是我们刚才生成的列表。

这个巢穴很容易从你身边逃脱。我尽量少用它。当我需要嵌套时，我在单独的行上写下每个列表理解并存储它...

```
the_list = [int(v) for v in input().split(' ')]
total = sum(n**2 for n in the_list)
print(total) 
```

Enter fullscreen mode Exit fullscreen mode

...测试一下，然后通过复制和粘贴开始嵌套。

## 生成器表达式中的条件

让我们把这个例子变得更复杂一些。如果我们只想要列表中奇数*的总和呢？令人惊叹的是，生成器表达式和列表理解也能做到这一点。*

最终，我们将在这个例子中使用*嵌套*，但是我们将首先从非嵌套版本开始，以使新的逻辑更容易理解。

```
the_list = [int(v) for v in input().split(' ')]
total = sum(n**2 for n in the_list if n%2==0)
print(total) 
```

Enter fullscreen mode Exit fullscreen mode

新零件在第二行。在生成器表达式的最后，我加了`if n%2==0`。您可能认识模运算符(`%`)，它给出了除法的*余数*。任何偶数都可以被`2`整除，这意味着它没有余数。因此，`n%2==0`仅对*偶数*数成立。

把条件句放在语句之后，而不是之前，感觉有点怪怪的。理解它的最简单的方法是考虑没有生成器表达式的相同代码看起来会是什么样子...

```
output = []
for n in the_list:
    if n%2==0:
        output.append(n**2) 
```

Enter fullscreen mode Exit fullscreen mode

基本上，要将它转化为生成器表达式，您只需在`append()`中抓取逻辑，将其放在前面...

```
n**2
for n in the_list:
    if n%2==0: 
```

Enter fullscreen mode Exit fullscreen mode

...然后从`for`和`if`语句中删除冒号(`:`)、换行符和缩进...

```
n**2 for n in the_list if n%2==0 
```

Enter fullscreen mode Exit fullscreen mode

## 多重迭代

我们也可以使用生成器表达式和列表理解来一次遍历多个 iterables，就像嵌套循环一样。

考虑以下逻辑:

```
num_a = [1, 2, 3, 4, 5]
num_b = [6, 7, 8, 9, 10]
output = []

for a in num_a:
    for b in num_b:
        output.append(a*b) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以按照我刚才给出的步骤，把它变成一个列表理解。我们把`append()`的论点放在前面...

```
a*b
for a in num_a:
    for b in num_b: 
```

Enter fullscreen mode Exit fullscreen mode

...然后我们把剩下的部分折叠成一行，去掉冒号。

```
a*b for a in num_a for b in num_b 
```

Enter fullscreen mode Exit fullscreen mode

最后，将它放在方括号中，并将其分配给 output。

```
output = [a*b for a in num_a for b in num_b] 
```

Enter fullscreen mode Exit fullscreen mode

# 设定理解

正如我在文章开头提到的，正如您可以使用方括号`[ ]`中的生成器表达式创建一个列表一样，您也可以使用花括号`{ }`创建一个**集**。

例如，让我们把 100 除以一个小于 100 的奇数得到的余数集合起来。通过使用集合，我们可以确保没有重复，使结果更容易理解。

```
odd_remainders = {100%n for n in range(1,100,2)}
print(odd_remainders) 
```

Enter fullscreen mode Exit fullscreen mode

运行这段代码给了我们...

```
{0, 1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 13, 14, 15, 16, 17, 18, 19, 21, 22, 23, 25, 26, 27, 29, 30, 31, 33, 35, 37, 39, 41, 43, 45, 47, 49} 
```

Enter fullscreen mode Exit fullscreen mode

这里真的没有任何惊喜。集合理解的工作方式与列表理解相同，只是创建了一个容器。

# 字典释义

字典理解遵循与其他形式的生成器表达式几乎相同的结构，但是有一点不同:T2 冒号 T3。

如果你还记得，当你创建一个集合*或者*一个字典时，你使用了花括号`{ }`。唯一的区别是，在字典中，您使用冒号`:`来分隔键值对，这是您在集合中不会做的事情。同样的原则也适用于此。

例如，如果我们想要创建一个字典，将 1 到 100 之间的一个整数存储为键，将该数字的平方存储为值...

```
squares = {n : n**2 for n in range(1,101)}
print(squares) 
```

Enter fullscreen mode Exit fullscreen mode

这就是全部了！同样，除了冒号`:`之外，其他一切都与任何其他生成器表达式相同。

# 危害

将列表理解或生成器表达式用于几乎所有的事情可能非常诱人。它们很容易让人上瘾，部分原因是人们在制作时会觉得自己很聪明。强大的一行程序让程序员非常兴奋——我们真的喜欢聪明地使用我们的代码。

然而，我必须警告你不要太疯狂。还记得 Python 的[禅吗！这是与这个话题相关的部分...](https://www.python.org/dev/peps/pep-0020/)

> 漂亮总比难看好。
> ...
> 简单比复杂好。
> 复杂总比复杂好。
> 扁平比嵌套好。
> 稀疏胜于密集。
> 可读性很重要。
> ...

列表理解可能是美好的，但如果使用不当，它们也可能变成密集的、剧毒的逻辑片段。

## 1。它们很快就变得不可读了

这个例子是我从[OpenEDX](https://openedx.atlassian.net/wiki/spaces/AC/pages/508461274/List+Comprehension+Examples)
的一项调查中借来的

```
primary = [ c for m in status['members'] if m['stateStr'] == 'PRIMARY' for c in rs_config['members'] if m['name'] == c['host'] ]
secondary = [ c for m in status['members'] if m['stateStr'] == 'SECONDARY' for c in rs_config['members'] if m['name'] == c['host'] ]
hidden = [ m for m in rs_config['members'] if m['hidden'] ] 
```

Enter fullscreen mode Exit fullscreen mode

你能看出是怎么回事吗？如果你读一会儿，你也许可以，但是你为什么要读呢？代码一清二楚。(在调查中，这被评为最不可读的例子。)当然，您可以添加注释来解释发生了什么——事实上，在最初的例子中他们就是这样做的——但是任何时候您需要注释来解释代码正在做什么，这几乎肯定是太复杂了。

列表理解和生成器表达式非常强大，但要让它们变得像这样不可读并不需要太多时间。

不一定非要这样。你可以简单地通过将你的列表分解成多行来重新获得可读性，就像传统的循环一样。

```
primary = [
    c
    for m in status['members']
        if m['stateStr'] == 'PRIMARY'
    for c in rs_config['members']
        if m['name'] == c['host']
    ]

secondary = [
    c
    for m in status['members']
        if m['stateStr'] == 'SECONDARY'
    for c in rs_config['members']
        if m['name'] == c['host']
    ]

hidden = [
    m
    for m in rs_config['members']
        if m['hidden']
    ] 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这并不能完全证明上述观点。我仍然使用传统的循环，而不是所示的例子，只是因为它们更容易阅读和维护。

还不相信这是 Python 的一个容易被滥用的特性？我的 IRC 好友`grym`非常友好地分享了他遇到的这个**真实世界**的例子。我们不知道它是干什么的。

```
cropids = [self.roidb[inds[i]]['chip_order'][
               self.crop_idx[inds[i]] % len(self.roidb[inds[i]]['chip_order'])] for i in
           range(cur_from, cur_to)] 
```

Enter fullscreen mode Exit fullscreen mode

光是看着它，我的灵魂就在燃烧。

## 2。它们不能代替循环

`grym`指出了下面的情景。(此代码是虚构的，仅供参考。)

```
some_list = getTheDataFromWhereever()
[API.download().process(foo) for foo in some_list] 
```

Enter fullscreen mode Exit fullscreen mode

对于外行人来说，这看起来无伤大雅，但是请注意发生了什么...**`some_list`中的数据被直接修改(变异)，但结果没有被存储**。这是一个列表理解，甚至是生成器表达式，被滥用来代替循环的例子。这使得阅读有些困难，更不用说调试了。

不管你想在生成器表达式上有多聪明，这是你应该坚持循环的一种情况:

```
some_list = getTheDataFromWhereever()
for foo in some_list:
    API.download().process(foo) 
```

Enter fullscreen mode Exit fullscreen mode

## 3。它们可能很难调试

想想列表理解的本质:你把所有的东西都打包到一个巨大的语句中。这样做的好处是，你消除了一堆中间步骤。缺点是...你消除了一堆中间步骤。

考虑调试一个典型的循环。您可以一次一个迭代地通过它，使用调试器来观察每个变量的状态。您还可以使用错误处理来处理不寻常的边缘情况。

相比之下，这些在生成器表达式或列表理解中都不起作用。所有东西要么有用，要么没用！您可以尝试解析错误和输出，找出您做错了什么，但我可以向您保证，这是一种令人困惑的体验。

你可以通过在你的第一个代码版本中避免列表理解来避免这种疯狂！使用传统的循环和迭代器工具，以显而易见的方式编写逻辑。一旦你知道它在工作，*然后并且只有在那时*你应该把逻辑折叠成一个生成器表达式，并且只有当你能在不回避错误处理的情况下这样做*。*

这听起来像是很多额外的工作，但我在竞争性代码高尔夫中遵循这种模式。我对生成器表达式的理解通常是我相对于缺乏经验的竞争对手的主要优势，但是我总是首先编写标准循环:我不能浪费时间调试生成器表达式中的错误逻辑。

# 回顾

列表理解、生成器表达式、集合理解和字典理解是 Python 令人兴奋的特性。它们允许您编写非常强大、简洁的代码。

然而，它们也不是没有局限性和缺点。在使用生成器表达式之前，您应该仔细权衡您的选项。即使您决定使用一个，最安全的做法是首先使用标准循环和迭代器*编写您的逻辑，然后将其重写为生成器表达式。*

让我们回顾一下要点...

*   生成器表达式遵循结构`<expression> for <name> in <iterable> if <condition>`。可选地，可以省略`if`部分。一个生成器表达式中可以使用多个`for...in`和`if`段。
*   您可以将标准循环和条件块更改为生成器表达式，方法是将最里面的代码移到前面，并删除剩余语句后面的冒号，通常将它们都移到一行。
*   允许嵌套的生成器表达式和列表理解。
*   列表理解产生一个列表。它是一个包含在方括号`[ ]`中的生成器表达式。
*   集合理解产生一个集合。它是一个包含在花括号`{ }`中的生成器表达式。
*   字典理解产生字典。它是一个包含在花括号`{ }`中的生成器表达式，表达式中的键-值对由冒号`:`分隔。
*   任何形式的生成器表达式都不能完全替代标准循环。在应用它们时要运用智慧，尤其是当它们很难阅读、理解或调试的时候。

生成器表达式只是**生成器**的冰山一角。我们将在下一节探讨这个话题。

像往常一样，我强烈建议您阅读文档:

*   [Python 函数式编程指南-列表理解和生成器表达式](https://docs.python.org/3/howto/functional.html#generator-expressions-and-list-comprehensions)
*   [Python 词汇表:生成器表达式](https://docs.python.org/3/glossary.html#term-generator-expression)
*   [Python 词汇表:列表理解](https://docs.python.org/3/glossary.html#term-list-comprehension)
*   [PEP 289 -发电机表达式](https://www.python.org/dev/peps/pep-0289/)
*   [Python 教程:数据结构-列表理解](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions)
*   [Python 参考:表达式-显示](https://docs.python.org/3/reference/expressions.html#displays-for-lists-sets-and-dictionaries)
*   人教版 274 -字典理解

* * *

*感谢`altendky`、`grym`和`nedbat` (Freenode IRC `#python`)提出的修改和加入建议。*