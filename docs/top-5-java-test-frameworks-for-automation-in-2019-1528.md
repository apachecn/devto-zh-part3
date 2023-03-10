# 2019 年自动化 5 大 Java 测试框架

> 原文：<https://dev.to/lambdatest/top-5-java-test-frameworks-for-automation-in-2019-1528>

几十年来，Java 一直是开发应用程序服务器端的最受欢迎的编程语言。尽管 JUnit 一直与开发人员一起帮助他们进行自动化单元测试，但随着时间的推移和测试的发展，当自动化测试正在兴起时，已经开发了许多基于 Java 的开源框架，并且在验证和业务逻辑方面与 JUnit 有很大不同。在这里我将谈论 2019 年使用 Selenium WebDriver 和 Java 执行测试自动化的 5 大 Java 测试框架。我还将强调这些顶级 Java 测试框架的独特之处。

## JUnit

Kent Beck 和 Erich Gamma 开发者 JUnit，这是 xUnit 的一个实例。它背后的主要目的是使 Java 开发人员能够编写脚本和执行可重复的测试用例。它通常用于测试小块代码。您还可以通过将 JUnit 与 Selenium web driver for Java test automation 集成来执行网站的自动化测试。每当添加任何新的代码时，您将被要求重新执行整个测试用例，并确保没有中断。

### 前提条件是什么？

该框架与 Selenium WebDriver for Java 高度兼容，因为 JUnit 是一个基于 Java 的框架，因此作为一些先决条件，您需要

*   在您的工作站上安装最新版本的 JDK。
*   下载最新版本的 JUnit 并设置环境。
*   精通面向对象编程语言的应用程序开发，尤其是 Java。

## 使用 JUnit 的优缺点？

JUnit 有几个优点。

*   在测试驱动的环境中工作的开发人员发现这非常有益，因为他们被迫阅读代码并发现是否有异常。
*   错误被及早发现，从而产生更可靠的代码。
*   开发可读性更好、无错误的代码可以增强信心。
*   使用最新版本的 JUnit(版本 4)，异常很容易识别。*甚至，您可以执行用旧版本 JUnit 编写的测试用例。
*   也可以配合 Java 5 或以上版本使用。
*   框架无法执行依赖项测试。这就是我们需要测试的地方。

JUnit 的唯一缺点是

*   框架无法执行依赖项测试。这就是我们需要测试的地方。

## JUnit 对你来说是顶级的 Java 测试框架吗？

JUnit 和 TestNG 都做同样的工作。除了不使用 JUnit 执行依赖测试以及参数化测试的实现过程在两种框架中不同之外，它们的特性几乎相同。此外，由于 JUnit 已经使用了很长时间，因此有了更好的社区支持，并且它已经被定义为使用 Selenium WebDriver for Java 的应用程序进行单元测试的标准。尽管 TestNG 的用户很少，但这个社区仍然非常庞大，并且在日益增长。因此，我们可以得出结论，对于 Java 测试框架，在 TestNG 和 JUnit 之间的选择完全取决于应用程序的性质和需求。

如果您希望开始使用 JUnit 作为您的 Java 测试框架，这里有一个使用 JUnit & Selenium 为浏览器兼容性执行[自动化测试的逐步过程。](https://www.lambdatest.com/blog/automated-testing-with-junit-and-selenium-for-browser-compatibility/?utm_source=Dev&utm_medium=Blog&utm_campaign=ar-03-031919us&utm_term=OrganicPosting)

## JBehave

我们都知道行为驱动开发或 BDD。这是一种以对业务用户透明的方式描述验收测试的测试。JBehave 是另一个用于 BDD 测试的 Java 测试框架，主要与 Selenium WebDriver for Java 一起使用。引入 JBehave 的主要目的是让新手能够容易地理解和熟悉 BDD。这是一种设计理念，使得应用程序的测试阶段更加基于其行为。

### 前提条件是什么？

使用 JBehave 的理想方式是与 Eclipse 集成。为此，除了以上的 JDK1.7 和 Indigo 以上的任何 Eclipse IDE，您将需要几个 jar 文件，如

*   Junit-4.1.0.jar
*   Jbehave-core-3.8.jar
*   2.4.jar
*   paraname-2.5 . jar
*   自由标记-2.3.9.jar
*   -= ytet-伊甸园字幕组=-翻译:粒粒粒尘紫月皮皮夏酷校对:阿衡时间轴:邦德猪
*   org . Apache . commons . collections . jar
*   Plexus-utils-1.1.jar

### JBehave 的优缺点

像所有其他 BDD 测试框架一样，JBehave 在许多方面也很有优势。

*   通过在从事不同项目的不同开发团队之间实现更好的协调，服务于行为驱动开发的最重要的目的。
*   项目经理和利益相关者可以更清楚地了解开发团队和 QA 团队的成果，因为规范有相似的格式。
*   产品有更好的规格，因为 JBehave 的特点是推理和对细节的思考。
*   JBehave 使用一种半正式的语言，它也有一个领域词汇表，这有助于在团队结构中保持一致的行为。

和其他 BDD 测试工具一样，JBehave 只有一个缺点。

*   BDD 测试工具的成功很大程度上依赖于项目中不同成员之间的交流，包括项目涉众、开发人员、测试人员以及组织的管理层。缺乏沟通可能会导致无法回答的问题和疑虑，最终可能导致错误的应用程序或不满足业务需求的应用程序，从而导致各方相互指责。

### JBehave 对你来说是顶级的 Java 测试框架吗？

JBehave 的工作方式与 Serenity 相同。然而，如果您的目标是使您的自动化验收测试更加有效，那么将 Serenity 与 JBehave 集成在一起以利用更好的测试体验是理想的。这是因为 Serenity 的核心概念是基于 BDD 开发的，它也使用户能够编写强大而丰富的测试报告。我们将在下一节对此进行更多的讨论。

## 宁静

Serenity 是一个用于行为驱动测试的开源库。该框架帮助您编写结构良好且更易于维护的验收标准。它扩展了 JUnit 和 WebDriver 的功能。最初，它被称为修昔底德。它改名的原因仅仅是因为发音困难。尽管如此，旧的名称可以使用，而指。

### 安装 Serenity 的前提条件是什么？

因为框架是基于 Java 的，所以你显然需要 Java 或任何其他面向对象编程语言的知识和经验。除此之外，在您的工作站中，您还需要

*   JDK 5 或更高版本。
*   Maven 3.0 或以上任何版本。
*   IDE 工具——Eclipse 通常是所有开发人员的首选，因为 Maven 和 Eclipse 的组合更容易使用。

### 宁静的优缺点

让我们来看看 Serenity 带给行为驱动开发世界的优势。

*   该框架帮助测试人员创建 REST 服务的测试用例。
*   用户不必花费大量时间来维护或构建他们自己的自动化框架。
*   它保持了一种平衡，而不是测试用例的定义和理解与其实现方式之间的界限。
*   它可以与许多自动化框架集成，如 Selenium、JBehave、Spring 等基于 Java 的高级开发框架，甚至可以与 JIRA 等持续集成工具集成。

Serenity 唯一的**缺点**就像 JBehave 一样。为了利用其支持行为驱动开发的完整特性，项目参与者之间需要不断的交流。

### Serenity 是不是最适合你的 Java 测试框架？

该工具主要用于报告验收标准。然而，使用 Serenity 编写的报告比 JBehave 或任何其他 BDD 框架信息丰富。它还帮助开发人员编写质量更好的自动化测试用例场景，并提供对 RestAssured 和 Selenium WebDriver 的支持，使测试人员能够更快、更简单地编写基于自动化的验收测试标准。

## TestNG

Cedric Beust 创建了 TestNG，这是一个开源的基于 Java 的自动化测试框架，灵感来自 JUnit。但是，它要强大得多。NG 在 TestNG 中的意思是下一代。该框架旨在集成过程中以更好的方式执行。该框架通过排序、分组和参数化等功能帮助开发人员，从而使新的测试用例更加灵活，并消除了以前使用的框架的缺点。

### 安装 TestNG 的先决条件

要安装 TestNG，您需要

*   最新版本的 JDK 安装在您的系统中。
*   Eclipse 的最新版本
*   Java 或任何其他面向对象编程语言的知识和实践经验

### TestNG 的优缺点

除了克服旧框架的缺点，TestNG 还有几个优点。

*   该框架使您能够在多个代码片段上运行并行测试。
*   在测试用例执行期间，您可以生成一个 HTML 报告。
*   测试用例可以根据优先级进行分组和排列。执行测试要简单得多，只需要求框架运行数据库测试或前端测试或任何您想要的测试。
*   您可以将数据参数化，并使用注释来轻松设置优先级。

唯一的缺点是

*   这取决于你的要求。如果您的项目不需要测试用例优先化，您可能不想使用 TestNG。此外，设置 TestNG 需要一些时间。

### TestNG 是不是最适合你的 Java 测试框架？

尽管 JUnit 执行所有与 TestNG 相同的功能，但是它有一定的局限性。当测试在孤立状态下进行时，JUnit 是完美的。但是当存在依赖关系时，您将无法控制首先执行哪个测试。TestNG 通过允许您以任何想要的顺序运行测试用例来帮助您。此外，它还有助于参数化测试。尽管这个特性在 JUnit 4.5 中已经存在，但是 TestNG 要有效得多。如果您的项目很复杂，并且您可能需要一百多个测试用例，那么最好花些时间设置 TestNG，而不是依赖 JUnit。

如果您是 TestNG 的新手，那么这里有一个完整的指南，指导您使用 Selenium WebDriver with Java 运行您的第一个 TestNG 自动化脚本,以执行自动化的跨浏览器测试。

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://accounts.lambdatest.com/register/?utm_source=Dev&utm_medium=Blog&utm_campaign=ar-03-031919us&utm_term=OrganicPosting)

## 硒化物

由 Selenium 提供支持，Selenide 是一个为 java 应用程序编写稳定、精确和富于表现力的 UI 测试用例的流行工具。测试像 Ajax 这样的现代 web 技术有一些复杂的问题，比如超时。WebDriver 是 UI 测试的流行工具，但是它缺乏处理超时的特性。硒化物以简单的方式处理所有这些问题。还有就是更容易抓，更容易学。你不需要对谷歌和搜索教程有任何要求。你所需要做的就是关注业务逻辑，只要几行简单的代码，你的工作就完成了。

### 使用硒化物的前提条件是什么？

硒化物的安装非常简单。如果您使用的是 Maven，您需要在 pom.xml 文件中添加以下几行。

```
<dependency>
    <groupId>com.codeborne</groupId>
    <artifactId>selenide</artifactId>
    <version>5.1.0</version>
    <scope>test</scope>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用的是 Ivy，在 ivy.xml 文件中，您需要添加

```
<ivy-module>
  <dependencies>
    <dependency org="com.codeborne" name="selenide" rev="5.1.0"/>
  </dependencies>
</ivy-module> 
```

Enter fullscreen mode Exit fullscreen mode

就这样，框架已经准备好让你开始测试了。

## 硒化的优缺点？

在应用程序的前端层，Java 在服务器端使用，测试人员面临的最常见的问题是超时。您编写的测试用例目前可能工作得很好，但是几天后，可能一些 Ajax 请求会比现在花费更多的时间，或者一些 Javascript 会运行得更慢。此外，您的系统可能同时运行另一个进程，导致您的测试用例失败。更不幸的是，你可能要花几天时间才能找到这些问题的根源。硒元素通过以下方式帮助您

*   简洁的测试用例编写过程，消除了超时问题。
*   支持使用 AngularJS 开发的应用程序的测试
*   减少了传统 Selenium 工具使用的大部分命令。

到目前为止，我们还没有发现使用硒化物有任何明显的缺点。如果您发现任何可能影响您工作的限制，请告诉我们。

### selenite 是不是最适合你的 Java 测试框架？

对于 UI 测试，除了 Selenium 的父框架 Selenium WebDriver，没有更好的基于 Java 的框架了。显然，WebDriver 无法解决 Ajax 超时或运行缓慢的 JavaScript 或任何需要花费时间加载的动态内容所导致的问题。为了克服这些问题，我们之前在测试用例中使用了“wait_until”或“sleep”方法。有了硒化物，我们再也不需要考虑那些问题了。只要关注业务逻辑，测试用例就会很好地服务于它们的目的。

如果您计划选择 selenie 作为自动化测试的 Java 测试框架，那么这里有一个指南可以帮助您使用 selenie、IntelliJ 和 Maven 运行 Selenium 自动化测试。

在当前的敏捷时代，开发人员也被要求参与测试。专业 java 开发人员和业余爱好者之间的区别在于他们对测试的了解和重要性的认识。您不一定要成为专家，但至少您应该具备编写能够自动验证代码的测试用例的基本知识。在这篇文章中，我们介绍了 5 种工具，它们是全球开发人员对应用程序进行 UI 测试、单元测试和 BDD 测试的首选。基于比较，我们可以得出结论，如果你对你的工作是真诚的，并且想成为一名专业的开发人员和测试人员，上面提到的工具在你的日常工作中会很有用。

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://accounts.lambdatest.com/register/?utm_source=Dev&utm_medium=Blog&utm_campaign=ar-03-031919us&utm_term=OrganicPosting)

原文出处:[lambdatest.com](https://www.lambdatest.com/blog/top-5-java-test-frameworks-for-automation-in-2019/?utm_source=Dev&utm_medium=Blog&utm_campaign=ar-03-031919us&utm_term=OrganicPosting)

**相关帖子**

1.  [自动化跨浏览器测试](https://www.lambdatest.com/blog/automated-cross-browser-testing/?utm_source=Dev&utm_medium=Blog&utm_campaign=ar-03-031919us&utm_term=OrganicPosting)
2.  【2019 年顶级 JavaScript 框架
3.  [52%的开发人员不知道的 JavaScript 风格](https://www.lambdatest.com/blog/a-javascript-flavor-52-of-developers-dont-know-about/?utm_source=Dev&utm_medium=Blog&utm_campaign=ar-03-031919us&utm_term=OrganicPosting)