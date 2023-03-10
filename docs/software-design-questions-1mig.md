# 软件设计问题

> 原文：<https://dev.to/programmingdecoded/software-design-questions-1mig>

我已经开始阅读一本关于[设计模式](https://refactoring.guru/design-patterns)的书，这让我想知道在考虑构建一个人的代码时应该问什么样的问题。最近，我开始将 Learnclick 代码从 CodeIgniter 迁移到 Laravel(目前只是为了练习)。在思考设计模式等问题时，我意识到我很容易被思考如何编写最易维护和最干净的代码的过程所麻痹。

我决定从某个地方开始，然后开始重构，因为我不想让方法变得太大。这就自动引出了一个问题，创建什么类，我是否应该使用[继承或组合](https://sergeyzhuk.me/2017/01/08/composition-over-inheritance/)。在研究设计模式时，有一点让我印象深刻，那就是人们应该总是试图为实现不同但工作相似的事物创建一个接口。接口是[面向对象设计](https://en.wikipedia.org/wiki/Object-oriented_design)的一个重要概念。然后我们可以开始考虑设计模式，但是它们应该更多的是一个指导方针，而不是一种精确的做事方式，就像[这篇文章](https://dev.to/geraldcroes/dont-use-design-patterns-8pn)有益地解释的那样。

还有很多东西需要学习，但我认为我设计的用于生成不同类型问题的 Learnclick 部分的新方法已经比我以前使用更过程化的编程风格时好得多。我过去的问题是，虽然我理解面向对象设计的原则，但我不能将猫和狗的例子转化为更抽象的概念。

<figure>[![](img/3cacc5832663a85a3f4ef2e878ecf0ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fiLbhipy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.programming-decoded.com/wp-content/uploads/2019/02/questions_UML.png) 

<figcaption>学习点击问题类 UML</figcaption>

</figure>

你如何着手设计你的软件代码？请留言评论。