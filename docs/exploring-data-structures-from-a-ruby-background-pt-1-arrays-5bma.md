# 探索 Ruby 背景下的数据结构。1:数组

> 原文：<https://dev.to/isalevine/exploring-data-structures-from-a-ruby-background-pt-1-arrays-5bma>

## 问题:通过查看 Ruby 的数据类型—从数组开始，我可以了解到关于数据结构和效率的什么信息？

现在我对 Ruby 和 Rails 都有了一些经验，并开始学习 JavaScript，我注意到自己对代码优化和效率重新产生了兴趣。早些时候，我被告知 Ruby 不是一种“高效”的语言，因为“太多的事情发生在幕后”,性能损失是易用性的代价。Christopher Watson 对 Crystal 的介绍强调了这一点，Crystal 是另一种受 Ruby 启发的语言，旨在提高性能，同时保持语法的易用性。

这种态度让我很惊讶！**不都是**计算价值效率吗？难道不是为了易用性而牺牲效率…嗯，只有像我这样的初学者才优先考虑？

*(我很好奇**你的**对这一点的想法，尤其是如果你已经编码很长时间了——欢迎在下面随意评论！)*

因此，我一心想着优化，我想尝试用我的 Ruby 知识来探索数据结构和算法。我认为“深入了解”Ruby 可以让我了解它是如何实现数据结构的，也可以让我了解更低层次的效率因素。在一位真正的计算机科学家的指导下(谢谢史蒂夫！)，我决定从研究数组开始，包括数据结构和 Ruby 对象。

## 数组容量和内存分配

[在](https://en.wikipedia.org/wiki/Array_data_structure) [数组](https://medium.freecodecamp.org/data-structures-101-arrays-a-visual-introduction-for-beginners-7f013bcc355a)上读取 [up](https://www.geeksforgeeks.org/introduction-to-arrays/) [，最初看起来](https://www.w3schools.in/data-structures-tutorial/data-structures-arrays/) [Ruby 数组](https://ruby-doc.org/core-2.4.1/Array.html)和数据结构完全一样:一个有序的集合，用一个整数索引。但是一些细节开始让我看到了不同之处。具体来说，我发现数据结构数组是一个“静态”数组，这意味着它的容量(它可以容纳的最大元素数)是固定的——数组 ***的总大小不能改变*** ！这在 Ruby 中是不可思议的，因为我们不断地修改数组(通常是它们的容量),而没有考虑到这一点！

数组如此“静态”的原因是因为内存分配。具有特定容量的阵列将需要占用适合该容量的单个内存块。(这就是为什么空数组还是会占用内存！)我们不能总是保证附近会有更多的内存来“添加”到原来的块上。因此，阵列的容量是静态的，以确保您不会陷入需要更多内存却无法获得的困境。

与静态数组不同，Ruby 的*数据类型*数组是一个“动态”数组——它的容量可以改变。那么 Ruby 的数组是如何做到这一点的呢？

感谢[这篇关于 Ruby](https://stackoverflow.com/a/26240433) 中数组和数据类型的 StackOverflow 讨论，我了解到 Ruby 源代码在其数组中内置了自动为不同大小的数组重新分配内存空间的功能。(我相信这发生在代码[中，就在这里](https://github.com/ruby/ruby/blob/dd2479bcea6cfbfb7656ef691ad5f9ff6ae543cd/array.c#L315)，但我不能肯定地说，因为我没有学过 c。)此外，Ruby 经过优化，为您提供了比您可能需要的更多的数组容量，因此您不需要经常为它重新分配内存。

有趣的是，从帕特·沙乌格内西关于 Ruby 中数据结构的精彩文章中可以看出这些容量变化的一个有趣的小事实:

> 随着您添加越来越多的元素，每当您跨越特定的数组大小时，Ruby 就会重复地增加数组容量的大小:3、20、37、56、85 个元素，等等。确切的阈值由 array.c 中的 ary_double_capa C 函数确定。阵列容量每次大约增加 50%，尽管第一次增加(从 3 增加到 20)是一种特殊情况。每次发生这种情况，您都要付出额外的性能代价。

我没想到会在 Ruby 的数组的“引擎盖”下找到*那个*！

要点 1:数据结构有严格的规则，高级语言所提供的灵活性必须明确地内置。对于阵列，这些规则之一是容量，因为这决定了阵列需要分配多少内存。Ruby 为它的数组建立了灵活性，因为你可能最终会想要调整它们的大小，而且不用担心改变数组容量的方法，继续编写 Ruby 代码会更容易。太好了！这至少解释了 Ruby 数组效率权衡的一个方面。

## 测试效率

我想进一步了解数组(Ruby 和数据结构类型)的一个方面是它们在不同任务中的效率。从我最初的研究中，我了解到所有的数据结构对于不同的操作(查找、追加、插入、删除)都有优点和缺点，但是因为我刚从 Ruby-land 来，并且主要从事小规模的代码工作(对我来说没有包含一百万个元素的数组！)，时差可以忽略不计。

[这个数组数据结构及其权衡的总结](https://www.interviewcake.com/concept/java/array)帮助我理解了我期望在 Ruby 数组中看到的基本优点和缺点——快速查找和追加，缓慢插入和删除。但是到目前为止，我还没有花时间真正地*测量【Ruby 的数组方法运行得有多快。(如果没有真实的例子和性能数字来看，真的很难概念化大 O 符号及其有用性！)*

因此，在我的 Flatiron 指导老师的帮助下，[我得到了一些代码](https://github.com/geluso/ruby_append_vs_insert_benchmark)，比较了在 Ruby 数组末尾添加项目(append，使用 Ruby 的< <方法)与在中间添加项目(insert，使用 Ruby 的)所需的时间。插入方法):

```
require 'benchmark'

ITERATIONS = 100

def add_to_end_of_list(array)
  array << 99
end

def add_to_middle_of_list(array)
  array.insert((array.length / 2).floor, 99)
end

factor = 1
while factor < 1_000_000
  loops = ITERATIONS * factor
  factor = factor * 10

  array1 = []
  array2 = []

  # benchmark adding to end of array
  start = Time.now
  loops.times do
    add_to_end_of_list(array1)
  end
  delta_append = Time.now - start

  puts delta_append * 1000.0

  # benchmark inserting into middle of array
  start = Time.now
  loops.times do
    add_to_middle_of_list(array2)
  end
  delta_insert_in_middle = Time.now - start

  puts delta_insert_in_middle * 1000.0

  puts
end 
```

程序简单地循环每个动作一定的次数(10，100，1000，直到 1_000_000)并打印出完成这个动作需要多少毫秒。这是我得到的:

[![list of benchmarks in milliseconds, showing append grows linearly and insert grows exponentially - the last line says “…I’m still waiting on the program to finish inserting one million items”](img/8dab67e831efe6f13582b91a7ca0de41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OFOPgZKF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tc54pllpezd5wg7njhy8.png)

几乎和预期的一样——随着迭代次数的增加，追加保持得很快，而插入明显变慢了。追加的时间以相对恒定的速率增长(10 倍的项目意味着大约 10 倍的所需毫秒数)，而插入的时间呈指数增长。(我最终放弃了等待 1_000_000 个插入的运行。)

对于数组来说，追加效率更高的原因是，在数组末尾添加一个新元素实际上只会改变数组中的一个元素；新元素之前的元素都不需要做任何事情。但是，当在中间插入一个元素时，后面的所有元素都必须将它们的索引号增加 1——随着数组的增长，需要更改索引的元素数量也在增长，因此指数级的跳跃以毫秒为单位。

这里有一个插入数组所需步骤的简单说明，[由 InterviewCake](https://www.interviewcake.com/concept/java/array#inserting) 提供:

[![screenshot of InterviewCake’s diagram on inserting into arrays, showing how the index numbers change for every element following the new one](img/cdd2b85553ba803ba93963b251c66a4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S068avNG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3g1qip1tzbdtntz5c9oo.png) 
*现在想象一下对一个有 1_000_000 个元素的数组做这些…那是一个 **lot** 的快速移动！*

**要点 2:数据结构的效率可以通过增加一个动作的执行次数(如追加或插入)来衡量，并测量所需时间的增长。这个增长的形状就是大 O 批注所指的！**看到这些数字，知道导致这些数字的行动，肯定会使大 O 符号和效率测量更加有形和易于理解。此外，它有助于进行一些编写这些基准测试的练习，并在更直观的层面上理解它们的结果——下一次我遇到一个巨大的数组并被要求在中间插入一些东西时，我肯定会记得等待 1_000_000 次插入运行时的无聊和沮丧！

## 总结和下一步

在正规的计算机科学教育之外学习数据结构确实令人望而生畏，但是我很高兴看到使用 Ruby 给了我一个了解它的一些基础知识的窗口！显然，我仍有许多需要学习和澄清的地方——以下是我希望继续探索的几个领域:

1.  链表——我知道 Ruby 没有显式地拥有它们，但是,[链表经常被描述成与数组相对比,](https://www.geeksforgeeks.org/linked-list-vs-array/),所以我想我有足够的上下文来开始理解在其他语言中使用链表的好处和代价。

2.  内存——我知道我在这篇文章中完全误用了内存的术语，特别是“块”！块、堆和栈都有非常具体的含义，我想更多地了解它们以及它们与具体数据结构的关系。

3.  Ruby 中的其他优化- [帕特·沙乌格内西的文章](https://www.sitepoint.com/rubys-missing-data-structure/)也提到了 Ruby 数组的“写时复制”优化，其中内存中的同一个数组为该数组的不同变体所共享。我隐约明白这是如何使用更少的内存并使在内存中查找更容易的，但正如我上面所说的:我知道我还有很多关于内存如何工作的知识要学习，优化阐明了它的许多核心原则！

4.  测量基准——我想在我的代码中有意识地建立效率，并且舒适地编写代码来测量特定的基准是其中很大的一部分。我期待着发展那种蜘蛛意识，知道测量什么，如何测量，为什么测量，以及最终做什么/重构来响应。

5.  大 O 符号——现在我有了一些说明 O(1)和 O(n)的代码，我可以开始看到大 O 符号传达效率的更大图景了！

## 学分和引文

特别感谢 Flatiron Seattle 的 Steve Geluso(我提到的那位*实际上的*计算机科学家)为学习数据结构以及 Ruby 基准代码提供了指导。

[https://www.geeksforgeeks.org/introduction-to-arrays/](https://www.geeksforgeeks.org/introduction-to-arrays/)

[https://en.wikipedia.org/wiki/Array_data_structure](https://en.wikipedia.org/wiki/Array_data_structure)

[https://hacker noon . com/crystal-ruby-syntax-with-almost-c-efficiency-ce 1 FB 9 c 977 b 8](https://hackernoon.com/crystal-ruby-syntax-with-almost-c-efficiency-ce1fb9c977b8)

[https://www . w3schools . in/data-structures-tutorial/data-structures-arrays/](https://www.w3schools.in/data-structures-tutorial/data-structures-arrays/)

[https://medium . freecodecamp . org/data-structures-101-arrays-a-visual-introduction-for-初学者-7f013bcc355a](https://medium.freecodecamp.org/data-structures-101-arrays-a-visual-introduction-for-beginners-7f013bcc355a)

[https://ruby-doc.org/core-2.4.1/Array.html](https://ruby-doc.org/core-2.4.1/Array.html)

[https://github . com/ruby/ruby/blob/DD 2479 BC ea 6 cf bfb 7656 ef 691 ad 5 f 9 ff 6a e 543 CD/array . c # L315](https://github.com/ruby/ruby/blob/dd2479bcea6cfbfb7656ef691ad5f9ff6ae543cd/array.c#L315)

[https://www.sitepoint.com/rubys-missing-data-structure/](https://www.sitepoint.com/rubys-missing-data-structure/)

[https://www.interviewcake.com/concept/java/array](https://www.interviewcake.com/concept/java/array)

[https://github.com/geluso/ruby_append_vs_insert_benchmark](https://github.com/geluso/ruby_append_vs_insert_benchmark)

[https://www.geeksforgeeks.org/linked-list-vs-array/](https://www.geeksforgeeks.org/linked-list-vs-array/)

#### 对于数据结构和算法的学习，有什么有帮助的建议，欢迎随时评论！