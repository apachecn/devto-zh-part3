# 如何使用 Gradle 运行分组测试

> 原文：<https://dev.to/automationhacks/how-to-run-your-grouped-testng-tests-using-gradle-4aoi>

介绍如何通过 gradle 对 testNG 测试进行分组和运行，并避免常见的陷阱。

<figure>[![](img/f6b4cb7014c6b10f8626070af9d1ad15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yGV_S2Rq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AoHUl3O7hD4k1VwvR) 

<figcaption>[照片由](https://unsplash.com/photos/46T6nVjRc2w) [汉娜·约书亚](https://unsplash.com/@hannahjoshua?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

大家好，

当构建一个测试框架时，要做的最关键的决定之一是在你的语言生态系统中选择测试框架。例如，在 Kotlin/Java 世界中，我们可以选择 JUnit、TestNG、Cucumber 或纯 Kotlin 框架，如 [kotlintest](https://github.com/kotlintest/kotlintest) 、 [spek](https://github.com/spekframework/spek) 。这些框架中的每一个都提供了一些基本的构造来实现类似的结果，以及它们自己的实现特性。

对于我目前的组织，我选择使用 TestNG 作为框架，因为它相当成熟，许多使用 JVM 语言的开发人员/测试人员已经很清楚它的不同特性，因此学习曲线较低。

一旦您连接了一些测试并且它们在 ide 中运行良好(在本例中是 IntelliJ ),下一个合乎逻辑的步骤显然是将它们提升到您的 CI env 中并通过 gradle 运行。在这样做的时候，我遇到了一个小问题，这个博客是教育其他人的一种方式，以便稍微了解我是如何调试并最终得出解决方案的。

### 分组测试:

任何测试框架的另一个经常使用的特性是它们支持将相似的测试组合在一起，并提供随意运行这个测试子集的能力。

让我们举个例子来更好地理解这一点

假设说，我们需要测试一个具有姓名和年龄属性的 Person 类，这样我们希望这个人有固定的姓名和年龄。我们还想运行属于 **person_test** 组的所有案例。

PersonTest.kt

```
import org.testng.Assert
import org.testng.annotations.AfterMethod
import org.testng.annotations.BeforeMethod
import org.testng.annotations.Test

class PersonTest {
    private var name = "Rob"
    private var age = 23

    @BeforeMethod()
    fun before() {
        println("Performing setup...")
        name = "John"
        age = 25
    }

    @Test(groups = ["person_test"])
    fun personNameTest() {
        Assert.assertEquals("John", name)
    }

    @Test(groups = ["person_test"])
    fun personAgeTest() {
        Assert.assertEquals(25, age)
    }

    @AfterMethod()
    fun after() {
        println("Performing teardown...")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 TestNG 中，我们可以向测试注释传递一个组名列表，以便在这个公共术语下唯一地标识它们。

```
@Test(groups = ["person_test"]) 
```

Enter fullscreen mode Exit fullscreen mode

下一步是建立一个简单的 **build.gradle** 文件，它应该能够运行这些测试。我们可以遵循如下的结构。注意:这个配置设置你的 IntelliJ 项目与 Kotlin
一起工作

```
plugins {
    id 'org.jetbrains.kotlin.jvm' version '1.3.31'
}

group 'com.example.testnggradle'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

tasks.withType(Test) {
    systemProperties = [
            tag: System.getProperty('tag', 'person_test')
    ]
}

task runTests(type: Test) {
    useTestNG() {
        testLogging.showStandardStreams = true
        includeGroups System.getProperty('tag', 'NONE')
    }
}

dependencies {
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk8"
    compile group: 'org.testng', name: 'testng', version: '6.14.3'
}

compileKotlin {
    kotlinOptions.jvmTarget = "1.8"
}
compileTestKotlin {
    kotlinOptions.jvmTarget = "1.8"
} 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的几件事是:

当运行作为 **-Dtag** 的 **runTests** gradle 任务时，我们想要传递一个命令行标志，以便能够运行属于我们想要的组的测试。

我们通过在任务中的 useTestNG()方法中添加下面一行来实现这一点:

```
includeGroups System.getProperty('tag', 'NONE') 
```

Enter fullscreen mode Exit fullscreen mode

为了确保我们所有的测试都能运行，我们将它默认设置为`person_test`。这可以设置为一个合理的默认值，如`regression`，以防您使用它来标记所有案例。

```
tasks.withType(Test) {
    systemProperties = [
            tag: System.getProperty('tag', 'person_test')
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们尝试通过命令行运行它，以执行最终检查，当我们在一些配置项上将它作为命令行作业推送时，它会正常工作。

```
./gradlew clean runTests -Dtag=person\_test 
```

Enter fullscreen mode Exit fullscreen mode

等一下。为什么下面失败了？如果您返回并查看 PersonTest.kt，您可以看到测试应该通过，因为我们在`@BeforeMethod`注释中为人名和年龄设置了正确的值。那么这里出了什么问题呢？

```
\> Task :runTests FAILED

Gradle suite \> Gradle test \> PersonTest.personAgeTest FAILED java.lang.AssertionError at PersonTest.kt:24

Gradle suite \> Gradle test \> PersonTest.personNameTest FAILED java.lang.AssertionError at PersonTest.kt:19

2 tests completed, 2 failed

FAILURE: Build failed with an exception. 
```

Enter fullscreen mode Exit fullscreen mode

让我们进入调查模式，使用`--info` CMD 线路开关运行相同的命令以获得更多信息。

```
./gradlew clean runTests -Dtag=person\_test --info 
```

Enter fullscreen mode Exit fullscreen mode

嗯（表示踌躇等）...

[![🤔](img/ffef9c186003c80fa92008f2bfd44e5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BKVM98FS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f914.png)

我们可以看到 name 和 age 的实际值仍然是初始值，并且没有打印出`println`方法的内容。这基本上暗示了 setup/teardown 方法没有得到执行，从而导致了这些失败。

```
Gradle suite \> Gradle test \> PersonTest.personAgeTest FAILED java.lang.AssertionError: expected [23] but found [25] at org.testng.Assert.fail(Assert.java:96) at org.testng.Assert.failNotEquals(Assert.java:776) at org.testng.Assert.assertEqualsImpl(Assert.java:137) at org.testng.Assert.assertEquals(Assert.java:118) at org.testng.Assert.assertEquals(Assert.java:652) at org.testng.Assert.assertEquals(Assert.java:662) at PersonTest.personAgeTest(PersonTest.kt:24)

Gradle suite \> Gradle test \> PersonTest.personNameTest FAILED java.lang.AssertionError: expected [Rob] but found [John] at org.testng.Assert.fail(Assert.java:96) at org.testng.Assert.failNotEquals(Assert.java:776) at org.testng.Assert.assertEqualsImpl(Assert.java:137) at org.testng.Assert.assertEquals(Assert.java:118) at org.testng.Assert.assertEquals(Assert.java:453) at org.testng.Assert.assertEquals(Assert.java:463) at PersonTest.personNameTest(PersonTest.kt:19)

2 tests completed, 2 failed 
```

Enter fullscreen mode Exit fullscreen mode

这对我来说是一个非常奇怪的问题，因为最初我无法理解为什么 testNG 和 gradle 会这样。

在谷歌搜索了几个小时，并在 GitHub、Stack Overflow 和 gradle docs 上运行了多个讨论之后。我最终找到了解决方案来解决这个问题，并获得我们想要的行为。

为了使安装/拆卸方法与上述配置一起工作，我们需要在它们中添加`alwaysRun = true`。

如果我们遵循 alwaysRun 的 [TestNG 文档，我们可以看到以下内容:](https://testng.org/doc/documentation-main.html#annotations)

*对于 before 方法(beforeSuite、beforeTest、beforeTestClass 和 beforeTestMethod，但不包括 beforeGroups):* ***如果设置为 true，则无论该配置方法属于什么组*** *都将运行。

For after methods(after suite，afterClass，...):如果设置为 true，即使先前调用的一个或多个方法失败或被跳过，也将运行此配置方法。*

下面是修改后的最终代码。

```
import org.testng.Assert
import org.testng.annotations.AfterMethod
import org.testng.annotations.BeforeMethod
import org.testng.annotations.Test

class PersonTest {
    private var name = "Rob"
    private var age = 23

    @BeforeMethod(alwaysRun = true)
    fun before() {
        println("Performing setup...")
        name = "John"
        age = 25
    }

    @Test(groups = ["person_test"])
    fun personNameTest() {
        Assert.assertEquals("John", name)
    }

    @Test(groups = ["person_test"])
    fun personAgeTest() {
        Assert.assertEquals(25, age)
    }

    @AfterMethod(alwaysRun = true)
    fun after() {
        println("Performing teardown...")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，gradle 构建和测试通过(通过安装和拆卸方法中打印的调试消息进行验证)

```
Gradle suite \> Gradle test \> PersonTest STANDARD\_OUT Performing setup...

Gradle suite \> Gradle test STANDARD\_OUT Performing teardown... Performing setup... Performing teardown... Finished generating test XML results (0.0 secs) into: .../build/test-results/runTests Generating HTML test report... Finished generating test html results (0.003 secs) into: .../build/reports/tests/runTests

:runTests (Thread[Task worker for ':',5,main]) completed. Took 0.527 secs.

BUILD SUCCESSFUL in 1s 3 actionable tasks: 3 executed 
```

Enter fullscreen mode Exit fullscreen mode

希望如果你有同样的问题，这个博客可以帮你节省一些时间。

如果您对达成此解决方案所参考的链接更感兴趣，您可以参考以下内容:

*   [分级文件—测试分组](https://docs.gradle.org/current/userguide/java_testing.html#test_grouping)
*   [GitHub](https://github.com/cbeust/testng/pull/1575)

就这样了，伙计们。下次见。快乐编码。如果你喜欢这个或者觉得其他人也能从中受益，为什么不与朋友或同事分享呢？