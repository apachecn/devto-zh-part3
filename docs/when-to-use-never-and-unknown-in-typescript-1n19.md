# 打字稿中何时使用“从不”和“未知”

> 原文：<https://dev.to/bnevilleoneill/when-to-use-never-and-unknown-in-typescript-1n19>

[![](img/8d705447fb752c8656996a9eea8ccd4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7iNlXCC5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_wh7P-jdH2o9xHgFi1bqbw.png)

在 Typescript [v2.0](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html) 和 [v3.0](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html) 中分别引入了 never 和 unknown 原语类型。这两种类型代表了类型理论的基本和互补的方面。Typescript 是根据类型理论的原则精心设计的，但它也是一种实用语言，它的功能都有实际用途——包括 never 和 unknown。为了理解这些用途，我们必须从这个问题开始，类型到底是什么？

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 用集合论解释类型

当你深入到一个基本定义时，一个*类型*是一组可能的值，仅此而已。例如，Typescript 中的类型字符串是所有可能字符串的集合。Date 类型是 Date 类的所有实例的集合(加上所有结构兼容的对象)，iterable 类型是为给定类型的迭代值实现 Iterable 接口的所有对象的集合。

Typescript 特别忠实于类型的集合论基础；在其他特性中，Typescript 具有联合和交集类型。像 string | number 这样的类型被称为“联合”类型，因为它实际上是所有字符串集合和所有数字集合的联合。

[![](img/5d2bfc5bab18335339c143bfc1daa543.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LYCwG25---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZUSJpOOStqTRvCZ8GucxSw.png) 

<figcaption>集合`string | number`包含了`string`和`number`集合。</figcaption>

因为 string | number 包含所有的 string 和所有的 number 值，所以它被称为 string 和 number 的超类型。

未知是所有可能值的集合。任何值都可以赋给未知类型的变量。这意味着 unknown 是所有其他类型的超类型。未知就是因为这个原因被称为*顶级*型。

[![](img/a46aa65afa1d7c83a01b967143cb8e1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q1Wh3dwB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AS0YZx_0dFeAvp2uB28MthA.png) 

<figcaption>集合`unknown`包含所有其他集合。</figcaption>

从来都不是空集。没有可以赋给 never 类型变量的值。事实上，将值的类型解析为 never 是错误的，因为这是一个矛盾。空集可以包含在任何其他集合中，所以 never 不是所有其他类型的子类型。这就是为什么 never 被称为*底*型。

[![](img/b7643268e8eaf6af4a03cf9591375b4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U17FI39e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuDmxpUKCQ8gQjxKPwlJHTg.png) 

<figcaption>空集，`never`，作为一个点存在于每隔一个集合内部。</figcaption>

底部和顶部类型的有用属性是分别相对于并集和交集操作的*标识元素*。对于任何类型的测试:

[https://medium . com/media/053176 a7 aedd 15d 6812008 a1 FB 02 FB 12/href](https://medium.com/media/053176a7aedd15d6812008a1fb02fb12/href)

这类似于一个数字加零不会改变它的想法，同样的道理也适用于一个数字乘以一。零是加法的单位元，一是乘法的单位元。

具有空集的并集不会增加任何东西，所以关于并集的恒等式也不会增加任何东西。交集选择两个集合之间的公共元素，但未知包含一切，因此未知是关于交集的同一性。

never 是类型联合中唯一一个可以“分解”的类型，这使得它在某些情况下是不可或缺的，我们将在下一节中看到。

### `never`是指从未发生的事情

让我们编写一些发出网络请求的代码，但是如果请求时间太长，就会失败。我们可以通过使用 Promise.race 将网络响应的承诺与给定时间长度后拒绝的承诺结合起来。下面是构造第二个承诺函数:

[https://medium . com/media/e0d 87 a 3378 e 46 b 11 D8 c 37672 cc9 d 8 fa 0/href](https://medium.com/media/e0d87a3378e46b11d8c37672cc9d8fa0/href)

注意返回类型:因为 timeout 从不调用 resolve，所以我们可以为 promise 类型参数使用任何类型，这样就不会有矛盾。但是最有效的类型是“永不”。(我所说的“最具体”是指代表最小可能值集的类型)。

现在让我们来看看超时的作用:

[https://medium . com/media/EB 86420 b 67d 315 e 1027 c 6462 AC 909215/href](https://medium.com/media/eb86420b67d315e1027c6462ac909215/href)

这个很好用。但是编译器如何从 Promise.race 调用中推断出正确的返回类型呢？race 返回一个承诺，第一个承诺的结果或失败将被解决。在这个例子中，Promise.race 的签名是这样工作的:

[https://medium . com/media/6b 24031 BD 871 ce 4 a 42 a 0d 0 E1 b 86d 43 e 6/href](https://medium.com/media/6b24031bd871ce4a42a0d0e1b86d43e6/href)

输出承诺中解析值的类型是输入解析类型的并集。上面的示例结合了 fetchStock 和 timeout，因此输入承诺解析类型为{ price: number }和 never，输出解析类型(可变库存的类型)应为{ price: number } | never。因为关于 unions 的恒等式永远不会简化为{ price: number }，这正是我们想要的。

如果我们在 timeout 中使用除 never 之外的任何类型作为返回类型的参数，事情就不会这么简单了。如果我们使用了 any，我们将失去类型检查的好处，因为{ price: number } | any 等同于 any。

如果我们使用 unknown，那么股票的类型将是{ price: number } | unknown，这并没有简化。在这种情况下，如果不进一步缩小类型，我们将无法访问 price 属性，因为 price 属性将只在 union 的一个分支中列出。

### 使用`never`删除条件类型

你会经常看到在[条件类型中使用](https://www.typescriptlang.org/docs/handbook/advanced-types.html#conditional-types)来删除不想要的情况。例如，这些条件类型从函数类型中提取参数和返回类型:

[https://medium . com/media/8fe 2464791 aa 7d 7c 9278 D6 fc 5659762d/href](https://medium.com/media/8fe2464791aa7d7c9278d6fc5659762d/href)

如果 T 是一个函数类型，那么编译器推断出它的参数类型或返回类型。但是如果 T 不是一个函数类型，那么对于参数或返回就没有合理的结果。我们在每个条件的 else 分支中使用 never，使这种情况成为错误:

[https://medium . com/media/462 cc 073 c 089 bb 21970 c 7 dcdf 1471 ACA/href](https://medium.com/media/462cc073c089bb21970c7dcdf1471aca/href)

条件修剪对于缩小联合类型也很有用。Typescript 的库包括不可空类型( [source](https://github.com/Microsoft/TypeScript/blob/eb2297df022f6b1f284aff96e93c06097bb01ea5/lib/lib.es5.d.ts#L1459) )，它从联合类型中移除 null 和 undefined。定义如下:

[https://medium . com/media/545142 cf 8 b 6d 2371 EC 38396 fc 7948 b 92/href](https://medium.com/media/545142cf8b6d2371ec38396fc7948b92/href)

这是可行的，因为条件类型[将](https://www.typescriptlang.org/docs/handbook/advanced-types.html#distributive-conditional-types)分布在类型联合上。给定任何形式，T 扩展 U？X : Y 当联合类型替换为 T 时，该类型扩展以将条件分布到该联合类型的每个分支:

[https://medium . com/media/9f 36a 600523919 c 465d 797433 Fe 731 da/href](https://medium.com/media/9f36a600523919c465d797433fe731da/href)

在每个联合分支中，T 的每一次出现都被被替换联合类型中的一个成分替换。如果 T 出现在 true case 而不是 false case 中，或者出现在更大的类型表达式中，这也适用:

[https://medium . com/media/589 DD FCA 83199 ee 38852 DAA 950234919/href](https://medium.com/media/589ddfca83199ee38852daa950234919/href)

所以像 NonNullable 这样的类型按照以下步骤进行解析:

[https://medium . com/media/041 de 067 e 436 a 891 a 2818 ea 6 a 84 fa 008/href](https://medium.com/media/041de067e436a891a2818ea6a84fa008/href)

结果是，给定一个非空的联合类型会产生一个潜在的缩小类型，使用 never 来删除不需要的联合分支。

### 使用`unknown`表示任何值

任何值都可以赋给未知类型的变量。因此，当一个值可能有任何类型，或者不方便使用更具体的类型时，请使用 unknown。例如，一个漂亮的打印函数应该能够接受任何类型的值:

[https://medium . com/media/0 D1 f 86 a 4235 da fee 2e 2275d 437 ab 7 b 8/href](https://medium.com/media/0de1f86a4235dafee2e2275d437ab7b8/href)

你不能直接用一个未知的值做很多事情。但是您可以使用类型保护来缩小类型，并对缩小类型上的代码块进行准确的类型检查。

在 Typescript 3.0 之前，编写 prettyPrint 的最佳方式是使用 any 作为 x 的类型，类型收缩与使用 unknown 的方式相同；因此，在 x 缩小为数组类型的情况下，编译器可以检查我们是否正确使用了 map 和 join，而不管我们使用 any 还是 unknown。但是，如果我们犯了一个错误，认为类型已经缩小，但实际上并没有缩小，那么使用 unknown 将会拯救我们:

[https://medium . com/media/6b 9 CB 629 f 21 a1 BF 0 ADF 40 ca 82 ea 197 e 7/href](https://medium.com/media/6b9cb629f21a1bf0adf40ca82ea197e7/href)

isarray 包不包含将 isArray 函数转换为类型保护的类型定义。但是我们可能在没有意识到细节的情况下使用 isarray。因为 isArray 不是类型保护，我们对 x 的类型使用了 any，所以 x 的类型在 if 体中仍然是 any。因此，编译器不会捕捉到这个版本的 prettyPrint 中的错别字。如果 x 的类型未知，我们将得到以下错误:

> *对象属于“未知”类型。*

此外，使用 any 会让您通过执行不一定安全的操作来作弊。未知让你保持诚实。

### 如何在`never``unknown``any`之间选择

prettyPrint 中的 x 类型和 timeout 的返回类型中的 promise 类型参数都是值可以具有任何类型的情况。不同之处在于，在超时中，承诺解析值可以是任何类型，因为它永远不会存在。

*   在没有或不应该有值的位置使用 never。
*   在有值的地方使用 unknown，但是它可能有任何类型。
*   除非你真的需要一个不安全的逃生出口，否则避免使用任何逃生出口。

一般来说，使用最具体的类型将工作。never 是最具体的类型，因为没有比空集更小的集合。unknown 是最不具体的类型，因为它包含所有可能的值。any 不是集合，它破坏了类型检查；所以，尽可能假装它不存在。

[1]: ^:在 never 和 null 之间有一个重要的区别:null 类型实际上是一个单元类型，这意味着它只包含一个值，即 null 值。一些语言将 null 视为所有其他类型的子类型，在这种情况下，它实际上是一个底层类型。(这包括未配置严格检查选项的 Typescript。)但是这会导致矛盾，因为，例如，null 实际上并不存在于所有字符串的集合中。所以请使用- strictNullChecks 编译器选项来获得无矛盾的 null 处理！

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *