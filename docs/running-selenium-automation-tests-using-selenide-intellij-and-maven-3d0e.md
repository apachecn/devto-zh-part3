# 使用 Selenium、IntelliJ 和 Maven 运行 Selenium 自动化测试

> 原文：<https://dev.to/muthuraja/running-selenium-automation-tests-using-selenide-intellij-and-maven-3d0e>

市场上有很多工具使用 Selenium 作为基础，并在其上创建一个包装器，以实现更多的定制、更好的代码可读性以及更少的维护，例如 Watir、量角器等。，要了解更多关于 Watir 的细节，请参考[使用 Watir 和量角器的跨浏览器自动化测试](https://www.lambdatest.com/blog/cross-browser-automation-testing-using-watir/?utm_source=Dev&utm_medium=Blog&utm_campaign=mu-14-080319us&utm_term=OrganicPosting)请参考[使用量角器的跨浏览器自动化测试& Selenium](https://www.lambdatest.com/blog/automated-cross-browser-testing-with-protractor-selenium/?utm_source=Dev&utm_medium=Blog&utm_campaign=mu-14-080319us&utm_term=OrganicPosting) 。

我们将看看另一个流行的库“Selenide ”,它使用 Selenium 作为自动跨浏览器测试的基础。

由 Selenium WebDriver 提供支持，使用 Selenium 进行测试是免费的，因为 Selenium 是一个用于自动化测试的开源库。事实上，这个库更关注自动化测试，尤其是简洁的 API 和稳定的测试。用硒化物测试硒已经非常方便和有效。

## 用硒元素测试硒的好处

**硒化测试支持多种语言:**

*   Java 语言(一种计算机语言，尤用于创建网站)
*   斯卡拉
*   绝妙的
*   Clojure

**整合选项有:**

*   可以使用 BDD
*   可以和 JUnit，TestNG，JBehave，Cucumber，Serenity 一起使用
*   可用于集成范围报告、诱惑报告等。,
*   支持跨浏览器并行执行
*   页面对象

要了解哪些公司使用硒化物进行硒测试，请参考此链接。

## 为什么我们还需要另一个包装？

围绕 Selenium WebDriver 有好几个测试库，但是是什么让 Selenium 用 Selenium 测试特别呢？

以下是用硒化物进行硒测试的一些主要吸引力。

*   简洁 UI 测试:selenite API 由非常少的构建在 Selenium 之上的类组成。实现自动化测试代码用于硒元素的硒测试是干净的，更好的可读性和易用性。

*   **AJAX 挑战**:AJAX 的妙处在于让网页从服务器检索数据，而无需加载整个页面。例如，如果您在表单中添加条目，假设在页面的左窗格中显示了最近添加的数据，那么整个页面不会刷新并返回数据。当在网页中实现这一点时，我们经常会以“没有这样的元素异常”而告终。为了解决这个问题，应该在 Selenium WebDriver 中应用不同等待方法。然而，当我们用硒元素执行硒元素测试时，这个问题已经解决了，不需要额外的代码。

*   **超时问题**:在运行自动化时，有时我们会遇到超时问题，例如，加载网页需要时间或者查找元素需要时间。这可以通过硒测试很好地解决，如果在构建方法中使用硒，默认情况下会等待 4 秒。除此之外，你还可以配置更多的秒数来实现更好的同步。

*   **自动截图** : Selenium 默认情况下使用 Selenium 进行测试，与 Selenium WebDriver 不同，它会对失败的测试用例进行快照。在 Selenium WebDriver 中，您需要添加显式代码。请看看下面的快照，它实际上拍摄了快照，并分享它所在的位置。

[![](img/004a72eee003a68af26e404d89fe9a78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y6pRLsRQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/pasted-image-0-39.png)

*   **StaleElementException** :这个异常发生有两个原因:
*   该元素不再附加在 DOM 中，但是您的代码可能已经引用了该元素
*   具有相同 id 或相同属性的元素已被完全删除或修改。
*   **专注于业务逻辑**:只需专注于您的业务逻辑需要测试的内容，其余的 Selenide 会处理，例如，条件、验证、同步、超时问题、浏览器配置等。,
*   **更加关注测试**:使用 Selenium 的 Selenium 测试更加关注自动化测试，在 Selenium WebDriver 之上提供了更好的便利性。![](img/8302443fa31e7fb8ef4858a46075ff5f.png)

由于 Selenium 是作为 Selenium 之上的一个包装器开发的，如果您已经使用过 Selenium 框架，那么这里有一些您可能会很熟悉的关键库:

| selenium-java:使用最新版本的 selenium Java API |
| selenium-server:支持 selenium 网格并行执行|
| webdrivermanager:支持内置浏览器配置以避免初始化|
| testng:支持 testng 框架|
| commons-fileupload:处理 servlet 和 web 应用程序的文件上传功能|
| junit:支持 junit 框架|

用硒元素测试硒的美妙之处在于:

*   您仍然可以直接使用 Selenium WebDriver
*   您可以使用任何 webdriver 实例

## 用硒化物启动硒测试

Selenide group 的 Selenium testing 提供了 3 个简单的东西来启动您的测试自动化，它们是:

*   打开页面
*   $(元素)。doAction()
*   $(元素)。检查(条件)例如，请查看下面的快照:

| SELENIUM WEBDRIVER | 硒化物 |
| --- | --- |
| driver . get("[https://www.lambdatest.com/](https://www.lambdatest.com/)) | 开([https://www.lambdatest.com/](https://www.lambdatest.com/)) |
| driver . find element(by . class name(" # submit "))。单击() | $("#submit ")。单击()； |
| string actual result = driver . find element(by . class name . message)。getText() | //条件 |
| If(text.contains(实际结果) | $(".message . ")。should has(text(" Hello ")) |

您可以观察到用于硒化物测试的易读和简洁的语法。

## 硒元素测试:设置环境

先决条件是 JDK 1.8.0 和 Intellij。
安装 Java，最低需要 JDK 8，在官方网站下载点击[这里](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

下载后，安装非常简单，无需解释即可完成。

安装完成后，您必须设置类路径，以便程序能够理解 java 在您机器中的位置

这可以在 windows 操作系统中通过搜索“环境变量”并选择“编辑系统环境变量”来完成。在系统变量和路径中创建 JAVA_HOME，如下图所示。

在系统变量中创建 JAVA_HOME，并更新 JAVA 在计算机中的位置的值。

[![](img/a8a26ddde3664fb909ad5e683b724b9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yCP0Fjla--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/003_Java_Home.png)

现在，我们需要编辑 Path 系统变量并在其中添加 JAVA_HOME，以便所有运行时程序都能够检测到机器中的 JAVA。确保在添加分号后追加该值。

[![](img/a5aebca1b3f4fe7978cfbab731bc2a57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e7J5JcMQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/004_Adding_JavaHome_in_Path.png)

验证 JDK 是否正确安装。您需要提供以下命令:
`java -version`

[![](img/75a744e4a5df21be83a53c14c8da0ef1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a-Ngkyi3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/005_Verifying_Java_Version.png)

太棒了。您刚刚在您的计算机上成功安装了 JDK。

## 用 Selenide 测试 Selenium 下载 IntelliJ 并设置 Maven 项目

市场上有多种 IDE 可供选择。在本文中，我推荐使用 IntelliJ 对硒元素进行硒测试。IntelliJ 是一个免费的开源 Java IDE(集成开发环境),用于开发基于 Java、Kotlin 的软件编程。这个工具有社区版和终极版，社区版的 IntelliJ 可以从这里下载。

下载 Intellij 后，安装部分非常简单，只需按照说明，继续使用默认参数并完成它。

打开 Intellij 并从仪表板窗口中选择“创建新项目”。

[![](img/f878a0485499591425091f12ba3d0b1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eJ6NsVPF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/006_IntelliJ_Launch_Page.png)

对于本文，我更喜欢使用“Maven”项目。选择我们安装的特定 Java 版本，并使用默认参数单击 Next。

[![](img/6ef1ccc18b3a96efb2927d07428fb352.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gqChks1q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/007_Select_Maven.png)

在 GroupID、ArtifactID 等必要字段中输入值，并将其保留为默认版本。

[![](img/f7c37e716f66ad86b923e6080a14d8ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bkEAFV59--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/008_projectName.png)

保留项目名称，并选择项目的位置。

如下所述，创建的用于硒化物测试的项目显示在 IntelliJ 中。
[![](img/922e51b2432d3bb462ffd11a64905e12.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--zi8MqSSB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/009_Maven_model.png)

添加硒化物 Maven 依赖，如下所述:

```
 <!-- https://mvnrepository.com/artifact/com.codeborne/selenide -->
    <dependency>
        <groupId>com.codeborne</groupId>
        <artifactId>selenide</artifactId>
        <version>5.1.0</version>
    </dependency>
</dependencies> 
```

请随意在类路径中添加所需的临时依赖项。正如我之前提到的，Selenide 库支持 TestNG、JUnit 框架。

**硒元素测试-样品代码(自我解释)**

```
package selenidesamples;

import org.openqa.selenium.By;
import org.testng.Assert;
import org.testng.annotations.Test;

import static com.codeborne.selenide.CollectionCondition.texts;
import static com.codeborne.selenide.Condition.text;
import static com.codeborne.selenide.Selenide.*;

public class LambdaSampleParallelTestAutomation extends BaseConfig_LambdaTest {

    /**
     * Author: Muthuraja R
     * Description: Validate Error message in Registration Page and capture one of error throws correct or not
     */
    @Test
    public void verifyStartTestingButton() {
        //Launch Lambdatest page
        open("https://www.lambdatest.com/");
        //use CSS Selector to find element and click for navigating register page
        $(".nav>li.login>a").click();
        //Verifying Page title using Selenide reusable method
        Assert.assertEquals("Sign up for free | Cross Browser Testing Tool | LambdaTest - LambdaTest", title());
        //Verifying text "Sign up"
        $(".signup-titel").shouldHave(text("SIGN UP"));
        //Feeding value to the text using .val
        //Feeding value to the text using sendkeys
        $(By.name("organization_name")).val("org");
        $(By.name("name")).sendKeys("Name");
        $(By.name("email")).sendKeys("test@gmail.com");
        $(By.name("password")).sendKeys("test1234");
        //using shortcut to signup instead of clicking button
        $(By.name("phone")).val("13452").pressEnter();
        //Capturing actual results and verifying with expected one using collections
        $$(".error-mass").shouldHave(
                texts("This email is already registered",
                        "Please enter a valid Phone number",
                        "To proceed further you must agree to our Terms of Service and Privacy Policy"));
    }

} 
```

在上面的例子中:

*   登录到 LambdaTest 页面。
*   点击注册按钮。
*   在必填字段的组织、全名、工作电子邮件、所需密码和电话中输入无效值，以确保返回错误。
*   最后，使用集合捕获页面中的所有错误。

与上一节相比，与硒相比，硒测试是轻量级的，易于使用，并提供更好的可读性。我没有包括浏览器配置或任何先决条件，如@BeforeClass 或@BeforeMethod，也没有使用循环来迭代和获取错误消息的内容。

要简单地运行您的本地浏览器，只需使用 url 并编写如上所述的基本测试。

默认浏览器是“chrome”浏览器，以防你没有声明任何配置。下面是没有浏览器配置的本地测试执行的测试结果。

[![](img/39d340336af48ab98a1bcb7890c3a2ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ajOGhg5V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/011_default_chrome.png)

[![](img/67cbc8e55e69cbfcee2303edb796bd82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MQe5lqlu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/010_Local_Instance_Test_Results.png)

## 希望在不同的浏览器上用 Selenide 进行 Selenium 测试？

在其他浏览器中运行相同的测试也非常简单。例如，如果你想使用火狐浏览器，我配置如下。

[![](img/10d2b5f9b718d08ac8d9b63d5928e990.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z0AymAjM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/Firefox_config.png)

要了解更多关于浏览器配置和如何知道硒支持哪些浏览器，请看看下面的快照。

[![](img/5318d5588c1c6649d9861b89a253704e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N9cvM-Qx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/013_More_Browser_config.png)

**使用硒化物测试硒化物的可重用命令**
这里我们深入探讨硒化物可重用命令:

```
package selenidesamples;

import org.openqa.selenium.By;
import org.testng.Assert;
import org.testng.annotations.Test;

import static com.codeborne.selenide.CollectionCondition.texts;
import static com.codeborne.selenide.Condition.text;
import static com.codeborne.selenide.Selenide.*;

public class LambdaSampleParallelTestAutomation extends BaseConfig_LambdaTest {

    /**
     * Author: Muthuraja R
     * Description: Validate Error message in Registration Page and capture one of error throws correct or not
     */
    @Test
    public void verifyStartTestingButton() {
        //Launch Lambdatest page
        open("https://www.lambdatest.com/");
        //use CSS Selector to find element and click for navigating register page
        $(".nav>li.login>a").click();
        //Verifying Page title using Selenide reusable method
        Assert.assertEquals("Sign up for free | Cross Browser Testing Tool | LambdaTest - LambdaTest", title());
        //Verifying text "Sign up"
        $(".signup-titel").shouldHave(text("SIGN UP"));
        //Feeding value to the text using .val
        //Feeding value to the text using sendkeys
        $(By.name("organization_name")).val("org");
        $(By.name("name")).sendKeys("Name");
        $(By.name("email")).sendKeys("test@gmail.com");
        $(By.name("password")).sendKeys("test1234");
        //using shortcut to signup instead of clicking button
        $(By.name("phone")).val("13452").pressEnter();
        //Capturing actual results and verifying with expected one using collections
        $$(".error-mass").shouldHave(
                texts("This email is already registered",
                        "Please enter a valid Phone number",
                        "To proceed further you must agree to our Terms of Service and Privacy Policy"));
    }

} 
```

在上面简单的可重用方法中，我们将看看什么是“$”和“$$”以及 selenium 操作和条件命令。

$和$$方法定义:

在硒化物术语中:

*   $ Indicate 查找单个元素
*   $$ Indicate 查找集合中使用的多个元素

[![](img/0476933f2deceef4cdbf47f52075d984.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uYm2ZJXi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/015_Element_Locators.png)

[![](img/4ce7001446dac67bb0b3b47f04c3cd18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DNU3ZtwE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/016_elements_locators_collections.png)

在上面的快照中，硒化物方法中的参数接受不同的类型:

*   webelemont _element)
*   字符串形式的 cssSelector
*   字符串形式的 Xpath
*   对于 cssSelector 和 xpath，您可以将 locator 直接指定为字符串，这样便于使用和维护定位器。

**条件:**
[![](img/4e504d3e02615f84bedac5d5365c56fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8rMVwEjT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/017_conditions.png)

在上面的示例程序中，我们使用以下条件来验证没有断言的结果:

*   $(".注册-titel”)。should has(text("报名"))；
*   在这个方法中，我使用“shouldHave”方法验证“注册”文本是否存在
*   ".注册标题”是 cssLocator
*   $表示查找/返回单个元素

*   $$(".误差-质量”)。shouldHave(texts("此电子邮件已注册"，"请输入有效的电话号码"，"另一个验证条件…. "));

*   使用 CSS 选择器定位返回多个错误的错误消息。

*   shouldHave 是验证必要文本出现的条件的方法。

*   texts 接受数组中的参数，在数组中可以传递多个预期的文本条件。

*   该方法应验证实际结果和预期结果之间的状况。

在 build 中，上述条件如 shouldbe，ShouldHave 展示了智能等待功能:

*   应该是(“可见的”)
*   should has(Text(" Hello Test "))
*   shouldNotBe("选择的")
*   应该(“消失”)

默认情况下，$。should()方法最多等待 4 秒钟。但是，如果您希望更改超时，您可以通过以下方式进行自定义:

*   Configuration.timeout=4000(毫秒)
*   mvn–dselenide . time out = 8000//mvn 特定命令

用于验证集合中的预期文本:

```
$(By.xpath("//p[@class='error-mass']")).shouldBe(Condition.visible).shouldHave(
        textCaseSensitive("This email is already registered",
            “Another Text”,
            “Another Text”)); 
```

更多功能:

|  |  |
| --- | --- |
| $(”div”)scroll to()； | $(“div”)。double click()； |
| $("div ")。inner text()； | $(“div”)。context click()； |
| $(“div”)。innerHtml()； | $(“div”)。hover()； |
| $(“div”)。exists()； | $("div ")。draganddrop()； |
| $(“选择”)。isImage()； | 变焦(3.5)； |
| $(选择”)。getSelectedText()； | $(“选择”)。getSelectedValue()；..更多的 |

## 跨浏览器并行测试自动化

到目前为止，我们已经看到了如何使用 Selenide 实现 Selenium 测试，并使用本地浏览器在单个实例上运行测试。

**问题陈述**:为了在多个浏览器及其多个版本中依次执行相同的测试，是一个繁琐且令人精疲力尽的过程。此外，尽早获得自动化测试结果反馈变得不可行。特别是，在 CI/CD( [持续集成和持续交付](https://www.lambdatest.com/blog/what-is-continuous-integration-and-continuous-delivery/?utm_source=Dev&utm_medium=Blog&utm_campaign=mu-14-080319us&utm_term=OrganicPosting))集成过程之后的敏捷发布窗口中。在您的本地系统中，您可以安装有限数量的浏览器，如果您希望使用 40-50 个甚至更多的浏览器版本，该怎么办呢？

**解决方案:**
**硒网格:**为了应对这一挑战，硒网格是一个解决方案。可以通过添加额外机器作为 selenium 节点来解决这个问题，在 selenium 节点中，我们可以利用所需的功能来传递适当的浏览器版本。然而，维护本地网格可能非常具有挑战性。这就是“云”解决方案发挥重要作用的地方。

一个免费的跨浏览器测试工具:LambdaTest 在一个包含 2000 多个真实浏览器和在线操作系统的 Selenium 网格上提供自动化和实时的交互式跨浏览器测试。LambdaTest 有能力选择多种操作系统及其浏览器的组合，以确保您的 web 应用程序无缝运行在不同的配置。在现实世界中，大多数测试都是在 Windows 环境下结合 Chrome、Firefox 等进行的。不同浏览器的 Linux 平台怎么样？带 IE 浏览器的 MAC OS 怎么样？你可以使用 LambdaTest 对[浏览器兼容性测试](https://www.lambdatest.com/feature?utm_source=Dev&utm_medium=Blog&utm_campaign=mu-14-080319us&utm_term=OrganicPosting)进行测试。

LambdaTest 还提供并行的跨浏览器测试自动化。我们可以在多个浏览器及其版本上运行一个自动化测试。

在下一节中，我将设置 Selenium Grid (Hub 和 Node ),以及如何使用 TestNG suite.xml 在 Chrome 和 Firefox 浏览器上并行运行测试

**Selenium 网格配置设置:**

*   请访问 seleniumhq 网站。
*   下载 Selenium Server 最新版本并存储在您舒适的文件夹位置。

[![](img/1451d29ecaf6ce499dc27c72bb5d3ee1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ccYA59KZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/image16-3.png)

要在本地运行 Selenium Grid，请在命令提示符下转到文件夹位置，并输入下面提到的命令。
`java –jar selenium-server-standalone-3.141.59.jar –role hub`

*   hub 表示作为主机并控制浏览器的服务器。Selenium 服务器成功启动，如下图所示。

[![](img/d1b33908397398391653b3c378b0b06a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oTR10AL5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/020_Selenium_Grid.png)

现在，轮到硒节点了。

Selenium 节点配置设置:

[![](img/6ad6ae1835f649b9d8bec333b977e31b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XLpCW1Jt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/021_Selenium_Node.png)

在上面的快照中，我下载了在远程实例中运行测试所需的 Chrome 驱动程序和 Firefox 驱动程序。我还创建了批处理文件，通过一次点击来运行配置，而不是每次打开命令提示符并输入命令。

要创建批处理文件，请输入以下命令。请确保扩展名必须是”。bat "
`java -jar selenium-server-standalone-3.141.59.jar -role node -hub`
[http://localhost:4444/grid/register](http://localhost:4444/grid/register)

要运行批处理文件，请在命令提示符下导航到特定文件夹，并输入“批处理文件的文件名”，如下图所示。

[![](img/a422613d63f4a8841161af14169f52b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--91LnMhs_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/022_Selenium_Node_start.png)

要验证网格控制台中浏览器实例的数量，请导航到 [http://localhost:4000](http://localhost:4000)

[![](img/a1fafbbf2e998a6a3e73c59ff2e1b087.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b-XBoQiD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/023_selenium_grid_Host.png)

默认存在 5 个火狐、5 个 Chrome 和 1 个 IE 浏览器。这取决于您所需的浏览器实例的数量，可以在 Selenium 节点配置中进行配置。

现在，是时候在跨浏览器平台中运行您的并行自动化了。

## 硒元素测试-样本代码(自我解释):跨浏览器本地并行自动化

在这个例子中，我将它保持为上一节中的相同类，但是有一点小小的修改，我在这个类和基类之间创建了继承关系。

派生类:

```
package selenidesamples;

import org.openqa.selenium.By;
import org.testng.Assert;
import org.testng.annotations.Test;

import static com.codeborne.selenide.CollectionCondition.texts;
import static com.codeborne.selenide.Condition.text;
import static com.codeborne.selenide.Selenide.*;

public class LambdaSampleParallelTestAutomation extends BaseConfig_LambdaTest {

    /**
     * Author: Muthuraja R
     * Description: Validate Error message in Registration Page and capture one of error throws correct or not
     */
    @Test
    public void verifyStartTestingButton() {
        //Launch Lambdatest page
        open("https://www.lambdatest.com/");
        //use CSS Selector to find element and click for navigating register page
        $(".nav>li.login>a").click();
        //Verifying Page title using Selenide reusable method
        Assert.assertEquals("Sign up for free | Cross Browser Testing Tool | LambdaTest - LambdaTest", title());
        //Verifying text "Sign up"
        $(".signup-titel").shouldHave(text("SIGN UP"));
        //Feeding value to the text using .val
        //Feeding value to the text using sendkeys
        $(By.name("organization_name")).val("org");
        $(By.name("name")).sendKeys("Name");
        $(By.name("email")).sendKeys("test@gmail.com");
        $(By.name("password")).sendKeys("test1234");
        //using shortcut to signup instead of clicking button
        $(By.name("phone")).val("13452").pressEnter();
        //Capturing actual results and verifying with expected one using collections
        $$(".error-mass").shouldHave(
                texts("This email is already registered",
                        "Please enter a valid Phone number",
                        "To proceed further you must agree to our Terms of Service and Privacy Policy"));
    }

} 
```

**基类:**

```
package selenidesamples;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Parameters;

import java.net.MalformedURLException;
import java.net.URL;

import static com.codeborne.selenide.Selenide.open;
import static com.codeborne.selenide.WebDriverRunner.setWebDriver;

public class BaseConfig_LambdaTest {
    private WebDriver driver;
    private DesiredCapabilities desiredCapabilities;

    public String getLambdaUserName() {
        return System.getenv("LAMBDA_USERNAME");
    }

    public String getLambdaPassword() {

        return System.getenv("LAMBDA_PASSWORD");
    }

    @BeforeClass
    @Parameters({"browserName", "platform", "browserVersion", "visualEnable"})
    public void init(String browserName, String platform, String browserVersion, String visualEnable) throws MalformedURLException {
        desiredCapabilities = new DesiredCapabilities();
        if (browserName.equals("chrome")) {
            desiredCapabilities.setCapability("browserName", "chrome");
        } else if (browserName.equals("firefox")) {
            desiredCapabilities.setCapability("browserName", "firefox");
        }
        desiredCapabilities.setCapability("platform", platform);
        desiredCapabilities.setCapability("version", browserVersion);
        desiredCapabilities.setCapability("visual", visualEnable);
        desiredCapabilities.setCapability("build", "LambdaTestBuild");
        desiredCapabilities.setCapability("name", "LambdaTest Execution");
        System.out.println("https://" + getLambdaUserName() + ":" + getLambdaPassword() + "@hub.lambdatest.com/wd/hub");
        driver = new RemoteWebDriver(new URL("https://muthu.crr:Kw8xyUoShEO9GAtPqW509OWHggQYzzaLJnznu9R3RChfRSnmhh@hub.lambdatest.com/wd/hub"), desiredCapabilities);
        //driver = new RemoteWebDriver(new URL("https://" + getLambdaUserName() + ":" + getLambdaPassword() + "@hub.lambdatest.com/wd/hub"), desiredCapabilities);
        setWebDriver(driver);
        open("https://www.lambdatest.com/");
        driver.manage().window().maximize();
    }

    @AfterClass
    public void tearDown() {
        if (driver != null)
            driver.quit();

    }

} 
```

## 什么是期望的能力？

它是存储浏览器属性、平台属性等的键/值对。例如，具体到什么浏览器名称、浏览器版本、平台名称，如 Windows 10、Linux 等。，这将决定浏览器在运行时的行为。

setCapability 方法有助于初始化并让“WebDriver”知道要运行哪个环境和浏览器。

理想能力的美妙之处在于，并行地在不同的浏览器中结合其版本和操作系统运行相同的测试用例。

在上面的例子中，我将解释我如何创建运行多个浏览器的样本测试。

Baseconfig 类是一个“基类”,它包含参数 BeforeClass 和 AfterClass，它们实际上是 TESTNG 的注释。要了解更多关于 TESTNG 的细节，请访问这里，但这个例子一步一步我是如何设计的样本测试。

步骤 1:使用如下的线程配置和参数化来配置 TestNG XML。
[![](img/5d7e536d0aefc1ca0e72f271dd1648be.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--N63xZ88O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/024_testng_parallel.png)

|  |  |
| --- | --- |
| 纱线支数 | 5(提及需要多少线程数) |
| 参数 | 初始化参数名及其值，例如 chrome 或 firefox |
| 班级 | 包含类名表示您想要执行哪个类 |

就并行运行测试而言，TESTNG 使您的生活更加轻松。在套件中的测试标签下配置相同的类，并确保在测试下添加的参数独立执行。

第二步:@参数名必须与 TestNG suites . XML
[![](img/0359c63ab82b01711539b8f477082f28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--csePRkyT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/025_baseConfig.png)中提到的名称相同

在多线程中执行这段代码时，会创建两个浏览器实例并并行运行您的测试。4

因为我们已经配置了 selenium 服务器，它正在运行。我们使用 RemoteDriver 来初始化服务器主机细节并配置所需的功能。

setWebDriver(driver):该方法对于初始化定制的浏览器配置非常重要，让 Selenide 框架中的 WebDriverRunner 运行，否则它将抛出超时错误，并认为默认浏览器(chrome)将在单个实例中执行测试。

下面是针对多个浏览器的并行执行的测试结果。

[![](img/2f21427990a890dccf03cbe7fa4105b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7U0XZVsc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/026_Parallel_Test_Results.png)

## 在 LambdaTest 中运行平行测试，用硒化物进行硒测试

在 LambdaTest 上并行执行相同的测试非常简单明了。我将在 LambdaTest 中解释相同的脚本，除了在 BaseConfig 类& TestNG 的代码中有一些调整。

[![](img/2c305440bbab0866423e5e7008e1bd92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mefC5cOg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/027_Parallel_Lambdatest.png)

在这个例子中，我添加了几个参数来在 LambdaTest 中运行相同的脚本。

|  |  |
| --- | --- |
| 浏览器名称 | 自定义方式来配置您的浏览器名称，如铬，火狐等 |
| 平台 | LambdaTest 提供在 Windows 10、Linux 等不同平台上运行。, |
| 浏览器版本 | LambdaTest 让你选择不同的浏览器版本，这实际上可以帮助你验证你的测试，比如你的产品是如何被客户使用的 |
| 视觉启用 | 这个功能基本上就是把所有的执行都记录在视频和日志里。 |

您还可以添加定制配置，请访问[此处](https://www.lambdatest.com/capabilities-generator/)。

[![](img/7112bd6775e535ea034e48d1ece21116.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vDYZin67--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/028_Capability_generator.png)

## LambdaTest 的 BaseConfig 类中的变化

[![](img/085a35d3cde8a286eac6eab70c4bb056.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--24vz60iZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/029_BaseConfig_LambdaTest.png)

在上面的代码中，我使用了两种方法，它们是:

*   getLambdaUserName()
*   getLambdaPassword()

这些方法实际上会从您在机器中设置的环境变量中获取 LambdaTest 用户帐户和密码。

## 如何为 LambdaTest 用户名/密码设置环境变量？

在 Windows 10 中->搜索环境变量->编辑系统环境变量->点击新建按钮

*   输入变量名“LAMBDA_USERNAME”
*   输入变量值作为“您的λ帐户”

[![](img/475abb48a3591b7fa53182f621ce0078.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tbx_JjRG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/image18-3.png)

**注:**

*   类似地，对密码进行同样的操作
*   API 密钥可以在 LambdaTest 登录的个人资料页面中生成
*   点击“确定”按钮
*   并行运行:
*   打开“testsuites_Parallel_Lambda.xml”。

[![](img/7c51a306d85f0c51c9f85e06e88f2530.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D1768oU5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/image27.png)

测试结果在 LambdaTest 门户中运行，结果可以显示在下面的快照中。

[![](img/d4b48f044175897ce983aee9b820f5fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ufh3QGvt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/032_Detail_Veiw_Test_Results.png)

导航到具体测试的详细视图，在那里你可以看到以下屏幕的参数。

*   摘要
*   例外
*   命令
*   网络
*   日志
*   [计]元数据

## 结论:

Selenide 是一个非常棒的库，它实际上解决了许多基于 Selenium 开发的问题。这个库帮助你实现你的测试，具有更好的可重用性和可读性。使用这个工具的好处是，您仍然可以编写 Selenium WebDriver 和 Selenium 代码，这使您的框架更加稳定和强大。

感谢您的时间，请让我们张贴您的反馈和意见。

原文出处:[lambdatest.com](https://www.lambdatest.com/blog/selenium-testing-with-selenide-using-intellij-maven/?utm_source=Dev&utm_medium=Blog&utm_campaign=mu-14-080319us&utm_term=OrganicPosting)

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://www.lambdatest.com/blog/wp-content/uploads/2018/11/Adword-Cyber2.jpg?utm_source=Dev&utm_medium=Blog&utm_campaign=mu-14-080319us&utm_term=OrganicPosting)

**相关文章**

1.  [使用 JUnit 和 Selenium 对浏览器兼容性进行自动化测试](https://www.lambdatest.com/blog/automated-testing-with-junit-and-selenium-for-browser-compatibility/?utm_source=Dev&utm_medium=Blog&utm_campaign=mu-14-080319us&utm_term=OrganicPosting)
2.  Selenium WebDriver 应该成为自动化测试首选的 13 个理由！
3.  [专业测试人员如何在 Selenium 自动化脚本中使用 CSS 选择器？](https://www.lambdatest.com/blog/how-pro-testers-use-css-selectors-in-selenium-automation-scripts/?utm_source=Dev&utm_medium=Blog&utm_campaign=mu-14-080319us&utm_term=OrganicPosting)
4.  [自动测试硒，黄瓜&测试 NG](https://www.lambdatest.com/blog/automation-testing-with-selenium-cucumber-testng/?utm_source=Dev&utm_medium=Blog&utm_campaign=mu-14-080319us&utm_term=OrganicPosting)