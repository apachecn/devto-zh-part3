# 突变测试介绍——或者为什么覆盖率很糟糕

> 原文：<https://dev.to/pedrorijo91/an-intro-to-mutation-testing-or-why-coverage-sucks-3anp>

> 这篇文章最初分享在我的个人博客上。请访问原始链接获取完整版本

我认为可以肯定的是，每个人都讨厌在半夜因为一些疯狂的生产错误而被吵醒。防止这些事故发生的最常用策略是对您的代码进行测试。如此努力地测试你的代码，也许你不会在凌晨 3 点被吵醒。

毫无疑问，自动化测试被认为是当今健全和低错误代码库的基础。您创建了一组测试(称为测试套件)，并通过反复运行测试套件来确保每次更改(或者至少在每次部署之前)的行为都是预期的。因此，团队想知道他们的测试有多好是很正常的:他们需要添加更多的测试吗，或者测试套件已经足够好了吗？

为了帮助回答这个问题，[代码覆盖率](https://en.wikipedia.org/wiki/Code_coverage)(通常缩写为 coverage)在很长一段时间内被视为评估测试质量的事实上的度量。但这是一个好的衡量标准吗？

### 什么是覆盖范围

我一直大力提倡让代码库具有强大的测试套件，这将在代码投入生产之前给我们很大的信心，相信我们的逻辑是正确的。和几乎所有人一样，我使用覆盖率作为衡量标准来理解代码是否需要更多的测试。

> 代码覆盖率是对测试套件中执行的代码行百分比的度量。

但是我很快就明白覆盖率并不是一个可靠的衡量标准。

覆盖率定义也有一些微妙的变化，它关注执行的功能、块、路径等的百分比。但是为了简单起见，让我们将定义保持在行数之下。

### 为什么覆盖率很差

也许你已经发现了覆盖率的问题。如果没有，让我们看看下面的代码片段:

```
public boolean biggerThanTen(int x) {
  if(x >= 10) {
    return true;
  } else {
    return false;
  }
} 
```

以及相应的单元测试:

```
@Test
public boolean myTest() {
  assertTrue(biggerThanTen(11));
  assertFalse(biggerThanTen(9));
} 
```

你能猜到代码覆盖率的价值吗？没错，百分之百！但是你可以很容易地看到我们没有检查边界值:如果我们把`10`作为参数传递，会发生什么？很明显，尽管覆盖了 100%,我们的测试套件有一个重大缺陷！

但是你想看到一个更令人沮丧的例子吗？:)

```
@Test
public boolean myTestV2() {
  biggerThanTen(11);
  biggerThanTen(10);
  biggerThanTen(9);
} 
```

该测试覆盖所有情况并报告 100%的覆盖率，但是...你有没有注意到我们完全忘记了断言函数返回值？相信我，这在现实生活中不会是第一次了！

### 突变检测

到目前为止，您应该确信代码覆盖率包含一些真正的问题。我们只看到两个案例，但我很确定我们可以找到更多。

那么我们有什么选择呢？

嗯，不久前我看到一个关于*突变测试* (MT)的演讲。[突变测试](https://en.wikipedia.org/wiki/Mutation_testing)可以看作是在代码上引入小的变化(突变)并针对突变的代码运行测试套件的想法。如果你的测试套件是强大的，那么它应该通过至少一个测试失败来捕捉突变。

MT 基于两个假设:

*   有能力的程序员假设指出由有经验的程序员引入的大多数软件错误是由于小的语法错误。

*   **耦合效应**断言简单故障可以级联或耦合形成其他突发故障。耦合效应表明，能够捕捉一级突变(单突变)的测试也将检测包含这些一级突变的更高级突变(多突变)。

#### 基本概念

在开始一些实际的例子之前，让我们先回顾一下突变测试的一些基本概念:

*   **变异算子/变异算子:**变异算子是应用于原始代码的运算。基本的例子包括用`'<'`替换`'>'`运算符，用`'or'`运算符替换`'and'`，以及替换其他数学运算符。

*   **突变体:**突变体是对一个实体(在 Java 中这通常是一个类)应用 mutator 的结果。因此，突变体是类的修改版本，将在测试套件的执行过程中使用。

*   **被杀死/幸存的突变:**当针对突变的代码执行测试套件时，每个突变体有两种可能的结果:突变体要么被杀死，要么幸存下来。一个**杀死的突变体**意味着至少有一个测试因突变而失败。一个**幸存的突变**意味着我们的测试套件没有捕捉到突变，因此应该改进。

*   **等价突变:**事物并不总是非白即黑。斑马确实存在！关于突变测试，并不是所有的突变都是有趣的，因为有些会导致完全相同的行为。这些被称为等效突变。等效突变通常揭示了可能被删除/简化的冗余代码。请看下面的例子:

```
// original version
int i = 2;
if (i >= 1) {
    return "foo";
}

// mutant version
int i = 2;
if (i > 1) { // i is always 2, so changing the >= operator to > will be exactly the same
    return "foo";
} 
```

### 针对 Java 的变异测试

对于 Java 语言，有一个很棒的 MT 框架叫做 [PIT](http://pitest.org/) 。在过去的几个月里，我一直在一个小项目中试验它，它捕捉到了一些有趣的案例。

坑为什么伟大？首先，它有一个 maven 插件(我知道，这听起来像是基本的，但大多数机器翻译工具更多的是研究风格，很难使用)。其次，效率极高。PIT 一直致力于让 MT 变得可用，并且在 IMHO 做得相当不错。

为了[在 maven 项目上开始使用 PIT](http://pitest.org/quickstart/maven/#getting-started),您只需要添加 maven 插件:

```
<plugin>
    <groupId>org.pitest</groupId>
    <artifactId>pitest-maven</artifactId>
    <version>LATEST</version> <!-- 1.4.5 at the time of writing -->
 </plugin> 
```

然后，通过运行以下命令，您可以在项目中使用 MT:

```
mvn org.pitest:pitest-maven:mutationCoverage 
```

检查在`<base_dir>/target/pit-reports/<date>`生成的 HTML 报告

对于一个给定的类，你会看到绿线表示突变体被杀死，或红线，表示没有测试覆盖该行或一个突变体存活。

### MT 问题

突变测试显然不是完美的。尚未被广泛使用的最大原因之一是这样一个事实，即在更大的代码库上需要强大的计算能力:许多可能的变种、许多测试、更多要编译的代码，等等。所有这些都大大增加了运行突变的时间。PIT 通过以下几个方面的优化来解决这个问题:

*   突变一代
*   突变体插入
*   测试选择
*   增量分析

另一个问题是，MT 可以很容易地创建突变体，这将使您的测试以无趣的方式失败，特别是如果您不按常规进行单元测试的话。例如，如果您使用 H2 数据库，MT 可以更改配置值，这会导致连接失败。模仿也是有问题的。

幸运的是，PIT 允许您指定哪些类和方法不应该变异，并且它支持所有主要的 java 模仿框架。但是其他 MT 工具可能没有 PIT 那么健壮。

### 极度突变

极端变异是另一种变异测试策略，以简化和提高机器翻译速度。它通过用一个可空的块替换整个方法逻辑来表现自己:在 java 中，我们没有关于`void`方法的代码，一个关于返回对象或返回一些常量的方法的简单的`return null;`语句。

极端突变的基础如下:

*   方法是对代码和测试套件进行推理的良好抽象层次；
*   极端突变产生的突变体比默认/经典策略少得多；
*   极端变异是在运行细粒度变异操作符之前加强测试套件的一个很好的初步分析。

以下代码片段显示了变异前的原始方法:

```
public Optional<String> getSomething(String baseStr) {
        if(baseStr.length() > 10) {
            baseStr += " -> this had more than 10 chars";
        }

        if(baseStr.startsWith("Why")) {
            baseStr += ", and it was probably a question";
        }

        if (baseStr.contains("<secret code>")) {
            return Optional.empty();
        } else {
            return Optional.of(baseStr);
        }
} 
```

极端突变会把它变成:

```
public Optional<String> getSomething(String baseStr) {
  return Optional.empty();
} 
```

对于 PIT 来说，有一个实现极端变异的引擎:[笛卡尔](https://github.com/STAMP-project/pitest-descartes)。你完全应该在你的项目中尝试一下，作为一个[更快的选择](https://github.com/STAMP-project/pitest-descartes/blob/master/docs/performance-comparison.md)。

### 结论

代码覆盖率有一些缺陷，不允许它成为测试套件有效性的真实来源。尽管如此，它仍然是一个危险信号:如果你有 10%的覆盖率，你显然没有对你的代码进行足够的测试(除非你有大量的样板代码——这也是一个危险信号)。

突变测试已经演变成一个真正的候选，成为评估测试套件质量的事实上的度量标准，挑战迄今为止一直由代码覆盖率占据的宝座。

尽管与这个概念相关的问题，像 PIT 这样的工具一直在创造解决方案，并使 MT 成为评估测试套件强度的可靠解决方案。

如果您有兴趣在其他编程语言上测试机器翻译，请看:

*   [突变](https://github.com/mbj/mutant)为红宝石
*   [stryker-mutator](http://stryker-mutator.io/) ，用于 Javascript、Scala 和 C#
*   [斯卡拉穆](https://github.com/sugakandrey/scalamu)，为斯卡拉

其他有用资源列表:

*   由它的创造者亨利·科尔斯所做的关于坑的介绍
*   [矿坑资源段](http://pitest.org/links)
*   [支持多模块项目 PIT 的 Maven 插件](https://github.com/STAMP-project/pitmp-maven-plugin)
*   [维修笛卡尔引擎](http://github.com/STAMP-project/pitest-descartes)
*   [Anthony Troy 在 MT 和 PIT 上发表的博客文章](http://www.notearsonlydreams.io/blog/2017/03/12/mutation-testing-is-awesome-pit-for-the-jvm/)

以及我在 Pixels Camp 2019 上做的一个演示:[https://speaker deck . com/pedrorijo 91/mutation-testing-Pixels-Camp-2019](https://speakerdeck.com/pedrorijo91/mutation-testing-pixels-camp-2019)