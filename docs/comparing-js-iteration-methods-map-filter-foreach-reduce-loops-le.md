# 比较 JS 迭代方法(map、filter、forEach、reduce + loops)

> 原文：<https://dev.to/manumagalhaes/comparing-js-iteration-methods-map-filter-foreach-reduce-loops-le>

**第三部【来自我第一个月编码的诡异问题】**

 *JavaScript 中有几种方法可以遍历数组。它们都有一个共同点:它们在数组的每一项中执行给定的函数；区别在于你得到的回报。

这里我假设你知道如何使用每个迭代器。不过，如果你被它们弄糊涂了，或者你不确定什么时候用它们也没关系。我花了几个星期才理解不同迭代器的正确使用，我希望这篇文章能帮助你搞清楚这一点。

<figure>[![](img/a96e1b2fcefec373bcd6d7d910b4dfcb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r5J2r8iN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/527/1%2AAgYSfRbK6jtTqiZosUp19g.png) 

<figcaption>学习 JavaScript 迭代方法时希望拥有的表格。</figcaption>

</figure>

### [T1。映射=转换](#map-transform)

当您想要在不改变数组原始值的情况下对其进行变换时，请使用 map。

[![](img/4bd4a954781efad71108e2edba9f17ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---QEc1rMQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/264/1%2AWOKdroo-6B0G6s0QJNssAQ.png)

(在现实生活中):你可以使用这种方法遍历 API 数据来显示你的网站中的每个条目——而不是在每个 div 中对它们进行硬编码。

### [T1。过滤器=选择](#filter-select)

当您想要**选择**数组中的某些元素时，请使用 filter。当一个元素满足函数中给定的条件时(换句话说，它是真的)，它将包含在返回的数组中:

[![](img/46f4ab71ac47afe681e0cfa7b8c14a2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kV6iOi6c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/290/1%2Asn_XOVfg0kxyDH1xv3Gh4A.png)

如果你有一个客户数据库，并且只需要找到 18 岁以上的客户，你可以使用过滤器来完成。

### [T1。forEach =执行](#foreach-execute)

当您只需要对数组中的每一项运行函数而不需要返回时，forEach 非常有用。

<figure>[![](img/4423cad1808b1284122a66cebd06672b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lrNbF5Ax--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/366/1%2Ar5ddWJiwKdxrHLWFbyQAxA.png) 

<figcaption>你可以看到 *oddsPlusOne* 正在处理所请求的函数，但最终，它是未定义的。</figcaption>

</figure>

IRL: 您可以使用 forEach 来处理和保存数据库中的数据。

### [T1。减少=减少](#reduce-reduce)

如果您需要将您的**数组简化为单个值**，reduce 是一个非常好的解决方案:

<figure>[![](img/c8e325cb5da5be6957ecc53fcff19b33.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IPVV5joQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/296/1%2AE4ztdJyxvRz9LYoe3ijCcg.png) 

<figcaption>本例中，函数为乘以 1×3×5×7</figcaption>

</figure>

**IRL:** 经典的例子是获取某个数组中数字的和。例如，如果您有一个 JSON 格式的图书目录，您可以查看您的祖母在您这个年龄时出版了多少本这样的书。

<figure>[![](img/9ee607b1f1463516293647e4cff7a4fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AZlE8Y8s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/707/1%2ArSeebWb5AllYnqdVzy2KEQ.png) 

<figcaption>表情符号能更好地解释吗？</figcaption>

</figure>

### for 循环=直到条件为假

当您知道要执行一个函数多少次时，使用这种迭代是很有用的。

<figure>[![](img/1f02e9eb38a5f366cd5d575dce1b530a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HL1gH04G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/189/1%2AMsaj45agPPDAh8nLxGQ1IQ.png) 

<figcaption>在这里，我们把 10 加到数字 9 上。当条件变为假(即 i=10)时，函数停止执行。</figcaption>

</figure>

### [T1】while 循环= while 条件为真](#while-loop-while-condition-is-true)

如果你不知道一个函数要执行多少次，while 循环可能是个好主意。例如，在现实生活中，这种迭代可以用来幻灯片显示你的手机图片，直到你点击停止。或者如果你的游戏角色只要你按下箭头键就一直在跑。在这两种情况下，你事先都不知道用户什么时候会中断循环。但是要小心:如果将 while 条件设置为 true，可能会意外地创建一个无限循环 Oo

[![](img/c973868ede5f1b8279db433e4d60f989.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XSb-UMfo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/480/0%2AmvOVwzZ4zjH2HsWz.jpg)

### 然而…

请注意，对于您的功能，可能没有一个正确的答案。如果仔细观察，您会发现 for 循环可以很好地完成 map 或 reduce 方法的工作，或者 while 循环可以很好地替代 for 循环。但是我们编码的越多，我们就越发现一些选项比其他选项更合适。您可能会选择某种方法来提高代码的可读性，或者选择另一种方法来提高性能。在任何情况下，如果你了解你的工具，你就有更多的选择来使你的代码对机器和你的人类同胞更有效和友好:)

> *“我编写代码第一个月遇到的奇怪问题”系列*
> 
> 第一部分:[数组索引&值如何在 for 循环中工作](https://medium.com/@manumagalhaes/how-array-indexes-values-work-in-for-loops-289163855f87?source=friends_link&sk=72f44ce790e8381c6ff36f69f444abda)
> 
> 第二部分:[如何编写进入过滤方法的函数](https://medium.com/@manumagalhaes/how-to-write-functions-that-go-inside-a-filter-method-ffefe1b83e9c?source=friends_link&sk=1c91261f94c8aeb8a7dcbc137277047b)

* * **