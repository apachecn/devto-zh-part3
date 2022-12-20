# 如何从 Java 中的单元测试开始:JUnit 5 完全介绍

> 原文：<https://dev.to/iuriimednikov/how-to-start-with-unit-testing-in-java-a-complete-introduction-to-junit-5-3cc>

你好，戴夫·托！在这篇文章中，我想重点谈谈用 JUnit5 库在 Java 中进行单元测试。与旧的 JUnit 4 版本相比，它引入了新的特性和测试方法，值得一试。我们将概述什么是单元测试以及为什么要测试；如何在你的项目中安装 JUnit 5；什么是基本的测试结构；如何使用 Asseritions API 以及如何在一个测试套件中组合多个测试。

顺便说一句，原始帖子发表在我的博客上，你可以在这里找到它

## 什么是单元测试？

单元测试是软件测试的一个层次，当我们孤立地测试单个软件的组件时。例如，我们有**用户服务**。它可能有各种连接的依赖项，比如 **UserDAO** 连接到数据源，或者 **EmailProvider** 发送确认电子邮件。但是对于单元测试，我们隔离了 **UserService** 并可能*模仿*连接的依赖关系(如何模仿，我们将在下一章看到)。

单元测试为我们提供了许多好处，仅举几个例子:

*   当我们改变代码时，它增加了我们的信心。如果单元测试写得很好，并且每次代码改变时都运行，当我们引入新特性时，我们可以注意到任何失败
*   它作为文件。当然，记录你的代码包括几种手段，单元测试是其中之一——它向其他开发人员描述了你的代码的*预期的*行为。
*   它让你的代码更加*可重用*，至于好的单元测试，代码组件**应该是**模块化的。

这些优势只是单元测试提供给我们的众多优势中的一小部分。现在，当我们定义了什么是单元测试以及我们为什么要使用它时，我们就准备好进入 JUnit5 了。

## 安装 JUnit5

### 构建工具支持

对于 JUnit5 的原生支持，您应该拥有 Gradle 4.6+或 Maven 2.22.0+版本。

对于 Maven，你需要添加到你的 *Pom.xml* :

```
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>{$version}</version>
    <scope>test</scope>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

对于 Gradle 添加到 *build.gradle* :

```
testCompile group: 'org.junit.jupiter', name: 'junit-jupiter-api', version: '$version' 
```

Enter fullscreen mode Exit fullscreen mode

你可以在官方库中找到 JUnit5 的最新版本。顺便说一句，如果你将 JUnit 5 与 Vert.x 框架一起使用，还有 [Vert.x JUnit5 扩展](https://www.mednikov.net/tutorials/start-with-vertx-junit5-at-no-time/)。

### IDE 支持

Intelij IDEA 从 2016.2 开始原生支持 JUnit5，从 4.7.1a 开始支持 Eclipse。

## 解剖单元测试

### 基本结构

考虑这个例子:我们有一个程序，它对一个整数数组中的一个数字执行*线性搜索。这里有一个主类，我们把它放在 *src/main/java/* 文件夹中:* 

```
class LinearSearcher(){

    private int[] data;

   LinearSearcher(int[] arr){
      this.data = arr;
   }

   int getPositionOfNumber(int value){
      int n = data.length; 
      for(int p = 0; i < n; i++) 
         { 
           if(data[p] == value) 
            return p; 
          } 
      return -1; 
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后将第二段代码添加到 *src/test/java* 文件夹:

```
class LinearSearcherTest{

    private static LinearSearcher searcher;

    //before
    @BeforeAll
    static void setup(){
       int[] array = {2, 3, 4, 10, 40};
       searcher = new LinearSearcher(array);
    }

    //Actual test methods
    @Test
    void getPosition(){
       int result = searcher.getPositionOfNumber(10);
       Assertions.assertEquals(3,result);
    }

    @Test
    void noSuchNumber(){
       int result = searcher.getPositionOfNumber(55);
       Assertions.assertEquals(-1, result);
    }

    //after
    @AfterAll
    static void finish(){
       System.out.println("Tests are finished!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们检查一下我们在这段代码中做了什么。我们引入了一个新的类， *LinearSearcher* ，它有一个方法- *getPostionOfNumber* ，该方法返回数组中值的位置，如果数组中没有值，则返回-1。

在第二节课中，我们实际上做单元测试。我们期望两种情况:当我们在数组中有一个数字(在我们的例子中是 10)时，我们期望接收它的位置(3)。如果没有出现这样的数字(例如 55)，我们的搜索器应该返回-1。现在，您可以运行这段代码并检查结果。

### 前法

你可以注意到两个分别用 *@BeforeAll* 和 *@AfterAll* 注释的方法。他们是做什么的？第一种方法对应于方法之前的**。他们有两个人:**

*   @BeforeAll -在当前类中的**所有** [@test](https://dev.to/test) 方法之前将执行一次的**静态**方法。
*   @BeforeEach -将在当前类中的每个 [@test](https://dev.to/test) 方法之前执行的方法。

这些方法便于设置单元测试环境(例如，创建实例)。

### [后](#after-methods)战法

由于在方法之前有*，在方法之后有**。还有几个例子:***

*   @AfterAll -在当前类中的所有**[@ test](https://dev.to/test)方法之后，**静态**方法将被执行一次。**
***   @AfterEach -将在当前类中的**每个** [@test](https://dev.to/test) 方法之后执行的方法。**

 **## 使用标准断言 API

断言 API 是一个支持在测试中断言条件的实用方法集合。有许多可用的方法，但是我们将集中讨论其中最重要的方法。

### 断言非空

当我们需要断言，那个*实际对象*不为空时，我们可以使用这个方法:

```
assertNotNull(Object obj); 
```

Enter fullscreen mode Exit fullscreen mode

如果 object 不为空，则该方法通过，如果不为空，则失败。

### 断言等于

这个组包括许多方法，所以我不会提供你的[所有重载版本](https://junit.org/junit5/docs/5.0.1/api/org/junit/jupiter/api/Assertions.html#assertEquals-byte-byte-)，而是集中一个通用签名:

```
assertEquals(expected_value, actual_value, optional_message); 
```

Enter fullscreen mode Exit fullscreen mode

这些方法有两个*必需参数*和一个*可选参数*:

*   expected_value =我们希望得到的结果
*   实际值=测试值
*   optional_ message =字符串消息，如果方法失败，将显示到 STDOUT。

值可以是基本类型:int、double、float、long、short、boolean、char、byte 以及 Strings 和 Objects。对于这个组，我们可以添加这些测试方法:

*   assertArrayEquals -检查预期数组和实际数组是否相等。数组属于基本类型
*   **AssertFalse** 和 **AssertTrue** -分别检查提供的布尔条件是假还是真
*   **assertiableequals**-与 assertArrayEquals 相同，但用于[可重复项](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html)(如列表、集合等)

正如我提到的，本节中有许多重载方法，因此值得研究一下官方文档中的具体签名。

### 断言抛出

这是 JUnit5 的一个创新。假设您有一个抛出异常的方法:

```
Car findCarById(String id) throws FailedProviderException; 
```

Enter fullscreen mode Exit fullscreen mode

该方法通过 ID 从底层数据库中检索单个*汽车*，并在数据库出现问题时抛出 FailedProviderException。换句话说，我们将可能的数据源异常(如 SQLException 或 NoSQL 数据库)封装在一个接口中，并实现其与实现的独立性。

我们如何测试异常被抛出？之前，在 JUnit4 中，我们使用了注释:

```
@Test(expected = FailedProviderException.class)
void exceptionThrownTest() throws Exception{
    Car result = repository.findCarById("non-existing-id");
} 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一句，同样的想法被用于测试。在 JUnit5 中引入了 *assertThrows* 方法。看一看，我们会如何处理同样的情况:

```
@Test
void exceptionThrownTest(){
    Assertions.assertThrows(FailedProviderException.class, ()->{
        Car result = repository.findCarById("non-existing-id");
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法签名有两个组成部分:

1.  预期将引发异常
2.  [*可执行*](https://junit.org/junit5/docs/5.0.1/api/org/junit/jupiter/api/function/Executable.html) 的 Lambda 表达式，包含一段代码片段，可能会抛出异常。

同样，正如我们前面提到的 assertEquals 组的方法一样，我们可以提供一个可选的消息字符串作为第三个参数。

### 断言超时

当我们需要断言，测试是在定义的超时内完成的，我们可以使用这个方法:

```
assertTimeout(Duration timeout, Executable executable) 
```

Enter fullscreen mode Exit fullscreen mode

这个想法与 assertThrows 方法相同，但是我们指定了*超时*。第二个参数是同一个可执行的 lambda 表达式。第三个可选组件是字符串消息。让我们考虑一个例子:

```
@Test
void in3secondsTest(){
   Assertions.assertTimeout(Duration.ofSeconds(3), ()->{
      //some code
   });
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这个方法使用*持续时间 API* 来指定时间范围。它有几个方便的方法，如 ofSeconds()、ofMills()等。如果你不熟悉它，[不要害羞去查看这个教程](https://docs.oracle.com/javase/tutorial/datetime/iso/period.html)。

### 失败

最后，如果我们需要测试失败怎么办？只需使用 *Assertions.fail()* 方法。还是那句话，有几个例子:

*   fail(字符串消息)=测试失败，并显示给定的失败消息。
*   fail (String message，Throwable cause) =测试失败，给出失败消息和根本原因。
*   fail (Throwable cause) =未通过具有给定潜在原因的测试。

## 创建测试套件

如果您有几个单元测试，并且您想在一次加载中执行它们，您可以创建一个*测试套件*。

这种方法允许您运行分布在多个测试类和不同包中的测试。

假设我们有 TestA，TestB，TestC，它们分别被分成三个包:net . mednikov . teststutorial . groupa，net . mednikov . teststutorial . groupa，net . mednikov . teststutorial . groupc。我们可以编写测试套件来组合它们:

```
@RunWith(JUnitPlatform.class)
@SelectPackages({net.mednikov.teststutorial.groupA, net.mednikov.teststutorial.groupB, net.mednikov.teststutorial.groupC})
public class TestSuite(){} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以将这个方法作为一个测试套件来运行。

## 参考文献

*   塞尔吉奥·马丁。*用 JUnit 5* (2018)让单元测试更上一层楼。DZone，[读到这里](https://dzone.com/articles/take-unit-testing-to-the-next-level-with-junit-5)
*   皮氏凯努莱宁。*用 JUnit5 断言 API 写断言*(2018)[看这里](https://www.petrikainulainen.net/programming/testing/junit-5-tutorial-writing-assertions-with-junit-5-api/)
*   史蒂文·佩里。*JUnit 5 Jupiter API*(2017)IBM 开发者，[在此阅读](https://developer.ibm.com/tutorials/j-introducing-junit5-part1-jupiter-api/)

## 结论

在这篇文章中，我们学习了什么是单元测试，为什么要测试；如何在你的项目中安装 JUnit 5；什么是基本的测试结构；如何使用 Asseritions API 以及如何在一个测试套件中组合来自不同包的多个测试。但是，当然，JUnit 5 是一个巨大的话题，这篇文章只是冰山一角。有些框架，比如 Vert.x，提供了特殊的 JUnit 5 扩展，比如 [vertx-junit5](https://www.mednikov.net/tutorials/start-with-vertx-junit5-at-no-time/) 。祝你六月五号好运！:)***