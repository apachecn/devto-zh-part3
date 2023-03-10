# 使用 TestNG 加速 Selenium 中的自动化并行测试

> 原文：<https://dev.to/lambdatest/speed-up-automated-parallel-testing-in-selenium-with-testng-471l>

如果手动执行，跨浏览器测试会变得紧张和耗时。想象一下在多种浏览器和版本上测试一个应用程序所需的人工工作量。事实上，当考虑多种浏览器与被测应用程序的兼容性时，你会觉得很有趣地相信大量的测试评估工作被考虑在内了。

这个问题不仅仅局限于手工操作，还需要一个合适的测试平台，包括各种平台、多种浏览器和它们的版本。

在敏捷环境中工作并不能让测试人员享受到在多种浏览器上手动执行跨浏览器测试的优势，因此只有有限的时间来选择哪些浏览器，而将其余的浏览器留在未测试区域，从而使您的应用程序面临测试风险。

在这样的场景中，您从头开始[自动化测试](https://www.lambdatest.com/blog/starting-automation-testing-from-scratch-here-is-what-you-need-to-know/?utm_source=hashnode&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=blog)，这有助于用最少的努力和时间在多个浏览器上测试您的应用程序。您可能熟悉通过自动化工具进行的[跨浏览器测试](https://www.lambdatest.com/?utm_source=hashnode&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=webpage)，但是您是否曾经探索过在 Selenium 中使用 TestNG 或您选择的任何其他框架进行自动化测试的同时确保浏览器兼容性测试的想法？

***看看这个，[柏树测试](https://www.lambdatest.com/cypress-testing?utm_source=devto&utm_medium=organic&utm_campaign=sep09_sd&utm_term=sd&utm_content=webpage)框架教程***

## 首先，什么是并行测试&它对我们有什么好处？

Selenium 中的并行测试帮助我们在自动化工具的帮助下，跨众多浏览器及其相应版本同时执行[自动化跨浏览器测试](https://www.lambdatest.com/?utm_source=hashnode&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=webpage)。它有助于在最短的时间内提供更好的覆盖，而不牺牲质量。Selenium 中的并行测试可以通过不同的自动化工具来实现。

在本文中，我将向您展示如何使用 TestNG 在 Selenium 中执行并行测试。

用 TestNG 在 Selenium 中实现并行测试有两种方法。**一个是在 testing.xml 文件中提及所需方法的“parallel”属性，另一个是在数据提供者注释方法中将 parallel 属性设置为 true。**

我将在本文中展示后者。为了使用 TestNG 在 [Selenium](https://www.lambdatest.com/selenium?utm_source=devto&utm_medium=organic&utm_campaign=sep09_sd&utm_term=sd&utm_content=webpage) 中运行并行测试，我们需要在多个平台上设置不同版本的多个浏览器并访问它们。这可以通过 [Selenium grid](https://www.lambdatest.com/selenium-grid-online?utm_source=hashnode&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=webpage) 并行执行来实现，使用它你将能够在众多浏览器、操作系统和设备组合上同时执行多个测试。

为了学习安装环境的安装和配置，请参考我们的第一个 Selenium & TestNG 自动化脚本的[指南。](https://www.lambdatest.com/blog/a-complete-guide-for-your-first-testng-automation-script/?utm_source=hashnode&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=blog)

带 TestNG 的 Selenium automation 中的并行测试帮助您同时在多个浏览器、操作系统和机器上运行测试。它使用了一个中心节点的概念，所有的测试都在一台称为中心的机器上运行，但是执行是由连接到中心的各种其他机器执行的，这些机器被称为节点，它们具有不同的平台和浏览器。网格设置中可以有多个节点，但只有一个集线器。使用 TestNG 在 Selenium 中进行并行测试有助于大幅减少执行时间。

为了在基于中枢节点的架构上设计脚本，我们需要了解两个重要的概念:

*   **期望能力**

*   **RemoteWebDriver**

## 期望能力

**DesiredCapabilities** 是**org . open QA . selenium . remote . desired capabilities 包**中的一个类。它有助于设置浏览器的属性，如浏览器名称、版本和平台。DesiredCapabilities 类的 **setCapabilityMethod()** 是在不同机器配置上使用 TestNG 或任何其他框架在 Selenium 中执行并行测试的重要方法之一。它有助于设置设备名称、平台版本和名称、被测应用程序的绝对路径等。**其他不同的 DesiredCapabilities 方法包括 getBrowserName()、setBrowserName()、getVersion()、setVersion()、getPlatform()、setPlatform()、getCapabilityMethod()等。**

展示下面的代码片段，用于设置浏览器、平台和版本号，以便在 Selenium 中使用 TestNG 执行并行测试:

```
 DesiredCapabilities capability= new DesiredCapabilities();
    capability.setPlatform(Platform.WIN8_1);
    capability.setBrowserName(“firefox”);
    capability.setVersion(“58”); 
```

Enter fullscreen mode Exit fullscreen mode

[https://www.youtube.com/embed/90F7dePW1vo](https://www.youtube.com/embed/90F7dePW1vo)

## RemoteWebDriver

RemoteWebDriver 是 WebDriver 接口的一个实现。其他实现包括 chromedriver，firefoxdriver，IEDriver 等。如果在本地运行您的测试，您选择除 remoteWebDriver 之外的上述其他驱动程序，而 remoteWebDriver 需要进行配置，然后才能在外部机器上运行。

RemoteWebdriver 帮助你连接到服务器，向它发送请求，然后驱动本地浏览器。 RemoteWebdriver 是一种客户端-服务器组合，可以在开发和执行环境运行在相同或不同的机器上时使用。当使用 TestNG 或您选择的任何其他框架在 selenium 中执行并行测试时，这是最好的选择。RemoteWebDriver 工作的唯一要求是，它应该指向网格的 URL。

因此，如果您使用除远程之外的任何驱动程序，则与 WebDriver 的通信被假定为本地的，例如:

**web driver driver = new Firefox driver()；**

上述语句将访问您本地机器的 firefox 浏览器，但是如果您使用 RemoteWebDriver，那么您需要提及 [Selenium Grid](https://www.lambdatest.com/selenium-grid-online?utm_source=hashnode&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=webpage)
的位置以及您倾向于使用哪个浏览器进行测试。例如:

**web driver driver = new remote web driver(新 URL("[http://localhost:](http://localhost:)8080/wd/hub ")，desired capabilities . Firefox())**

上面的语句表示您的 selenium 服务器运行在本地主机上，端口为 8080，将要实例化的浏览器是 firefox。您所要做的就是更改您希望指向并运行您的测试的机器的 URL。

任何**本地驱动**和 **remoteWebDriver** 的主要区别在于浏览器在本地打开，你可以查看你的脚本执行的步骤，而在 **RemoteWebDriver** 的情况下，你看不到浏览器打开并执行不同的操作。

***检查完这个，[柏自动化](https://www.lambdatest.com/blog/cypress-test-automation-framework/?utm_source=devto&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=blog)测试教程***

## 在 Selenium 网格中执行并行测试的问题

selenium grid 中并行测试的主要缺点是限制了对多个平台、浏览器和版本的访问，这有助于跨浏览器测试。您只能访问运行您的节点的那些平台、浏览器及其特定版本，最终，通过 hub 连接的节点越多，Selenium grid 的性能就会急剧下降。

此外，还需要花费时间和精力来建立分布式测试的初始实现。此外，设置多台机器的成本是选择在 selenium grid 中进行并行测试的另一个缺点。

为了遏制这些缺点，人们现在转向基于云的平台来支持 Selenium grid 中的并行测试。市场上有许多平台，它们提供了一种在一个地方访问许多浏览器、版本和平台的方法，不需要架构设置，也不需要为机器设置承担额外的成本。

**一个这样的平台是** [LambdaTest](https://www.lambdatest.com/?utm_source=hashnode&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=webpage) ，它有助于在一个可扩展、安全和可靠的基于云的 selenium 网格上运行 selenium 脚本，该网格可以访问大约 3000 多个浏览器，并且具有多种平台的不同分辨率。它通过屏幕截图、控制台日志、视频、网络等支持，提供对您的测试的详细访问。它让您的自动化流程更加顺畅，在旅途中访问与多个错误跟踪工具集成的[，如**吉拉、slack、trello 等**。一个详细的仪表板，为您提供关于运行的测试数量、并发会话、分钟和队列等的概览。使用 LambdaTest，您可以使用 TestNG 轻松地在 Selenium 中执行并行测试。](https://www.lambdatest.com/integrations?utm_source=hashnode&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=webpage)

[![](img/3a6c895316a0e21d507504e0569d71c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fECwH3oi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2Aff6HlPTzjgJPqGof.png)

通过 TestNG 认证，您可以挑战自己在使用 TestNG 执行自动化测试方面的技能，并让您的职业生涯更上一层楼。

以下是来自 LambdaTest 的 TestNG 认证的简要介绍:

[https://www.youtube.com/embed/dzXX2hJhuCY](https://www.youtube.com/embed/dzXX2hJhuCY)

现在，让我们深入研究如何使用云上的 TestNG 在 Selenium 中执行并行测试。

## 在 Selenium 中执行并行测试

为了进行引导，我构建了 Selenium 脚本，它可以在用户在 google 上键入“LamdaTest”后验证 LamdaTest 主页是否打开。启动该流程需要以下步骤:

*   一个 LambdaTest 帐户-你可以在这里注册。他们根据您的需求提供了多种套餐，您可以在这里查看

*   要连接的用户名、访问密钥和 URL。

*   设置 Selenium jars、testNG 和您选择用来编写测试的平台。

注册并登录 lambdatest 平台后，您可以从个人资料部分下的设置选项卡中访问您的用户名和访问令牌。第一次可能需要生成访问密钥。一旦生成了，你就可以复制它们并保存起来，以便在你的脚本中使用。

[![](img/5a46d48efeaeaedebf7a24ea76486c1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iuCi8Jpx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2562/0%2AMTTTLQkl_aixZuRL.png)

下面的代码片段强调了 Selenium 中带有 [TestNG 注释](https://www.lambdatest.com/blog/complete-guide-on-testng-annotations-for-selenium-webdriver/?utm_source=hashnode&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=blog)和断言的并行测试是如何通过云在多个浏览器和平台上运行的。

```
import org.openqa.selenium.By;
import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.Keys;
import org.openqa.selenium.Platform;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.openqa.selenium.support.ui.WebDriverWait;
import org.testng.annotations.AfterTest;
import org.testng.Assert;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;

import java.net.MalformedURLException;
import java.net.URL;
import java.util.concurrent.TimeUnit;

public class Cross_browser {

    public static final String  username= "sadhvisingh24";
    public static final String auth_key = "12345678910";
    public RemoteWebDriver driver;
      public static final String URL= "@hub.lambdatest.com/wd/hub";

      @Test(dataProvider= "Set_Environment")
      public void login(Platform platform_used, String browser_Name, String browser_Version) 
        {

            DesiredCapabilities capability= new DesiredCapabilities();
        capability.setPlatform(platform_used);
          capability.setBrowserName(browser_Name);
        capability.setVersion(browser_Version);
        capability.setCapability("build", "cross_browser");
        capability.setCapability("name", "cross_browser");
        capability.setCapability("network", true);//to enable network logs
        capability.setCapability("visual", true);//to enable screenshots
        capability.setCapability("video", true);//to enable video
        capability.setCapability("console", true);//to enable console logs

         try {

         driver = new RemoteWebDriver(new URL("https://" + username + ":" + auth_key + URL), capability);

             } 

       catch (Exception e) {

           System.out.println("Invalid grid URL" + e.getMessage());
             }

             try
            {
              driver.manage().timeouts().implicitlyWait(30,TimeUnit.SECONDS);
            driver.get("https://www.google.com/");
                   driver.findElement(By.xpath("//input[@class='gLFyfgsfi']")).sendKeys("lambdatest", Keys.ENTER);
               driver.findElement(By.xpath("//*[@id='rso']/div[1]/div/div/div/div/div[1]/a")).click();

               String url= driver.getCurrentUrl();
               Assert.assertEquals("https://www.lambdatest.com/", url);

               System.out.println("I am at Lamdatest page");

               }
            catch (Exception e) {
         System.out.println(e.getMessage());
                }
    }   

    @DataProvider(name="Set_Environment", parallel=true)
    public Object[][] getData(){

    Object[][] Browser_Property = new Object[][]{

    {Platform.WIN8, "chrome", "70.0"},
    {Platform.WIN8, "chrome", "71.0"}
    };
    return Browser_Property;

    }

    @AfterTest
    public void tearDown(){

            driver.quit();
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我定义了包含用户名、访问键、要连接的 URL 的变量，并使用了 RemoteWebdriver。一个名为**‘get Data’**的方法已经用数据提供者注释进行了定义，其中提到了不同的平台、浏览器和我希望用来测试我的脚本的版本。

请注意为扮演关键角色的数据提供者注释定义的属性。正如我上面提到的，这里我将属性**parallel 设置为 true** ，以便在 Selenium 中使用 TestNG 在定义的浏览器平台集上执行并行测试。

现在出现了主要的 **[@test](https://dev.to/test)** 带注释的方法，它接受 dataprovider 方法返回的参数。在这个方法中， **desiredcapability** 类用于设置 LambdaTest 提供的平台、浏览器名称、版本和其他一些主要属性，如构建名称、测试名称等。这也有助于为你的测试设置参数，如视频、日志、截图等，以备不时之需。您可以使用 [LambdaTest 功能生成器](https://www.lambdatest.com/capabilities-generator/?utm_source=hashnode&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=webpage)设置您想要的功能。

[![](img/93a680df22d5c9a1ddbe2a0aebdc8f18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z_Y8ppDG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2696/0%2AeG2jKirEAvX1H-QF.png)

使用 remoteWebDriver，您可以设置希望连接的本地主机，在我们的例子中，它是用户名、访问密钥和 URL 的组合。然后是对 LambdaTest 主页的简单验证。

一旦脚本完成，您就可以运行它了。正如我提到的，您可能无法在浏览器中看到操作，但是您可以在 LambdaTest 仪表板上看到测试完成后的状态。

下面是 testNG 报告执行后的屏幕截图:

[![](img/27a3d0315bbf1a95d0daa4261fbe4823.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y-hUtzrF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2732/0%2A_CWxvgxN6qQLC_0s.png)

下面是 LambdaTest 构建细节的截图，展示了在不同平台上执行测试的结果。您可以运行视频来观看脚本执行的步骤:

[![](img/9d02e1b9e8414f117279a707f7cf850b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DQruCE6F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2732/0%2AYprS_DtocXPTuGbf.png)

显示请求发送及其相应响应的网络选项卡屏幕截图:

[![](img/5f12fa23ce333815dfed63b296e74438.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CJwnbE0v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2732/0%2ACzVlbZ-b9_JLJH4r.png)

日志选项卡:

[![](img/ac9f935736d5ff61c2b5fbedea78494c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n1uFLVIj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2732/0%2A5QE7ES3Rg44D56WE.png)

显示运行的自动化套件数量的自动化时间表屏幕截图:

[![](img/0a143c824082bfec4c78514b68a9f1ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--25HWRtNq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2732/0%2ASFjjG6mEiozQk1Rr.png)

automation analytics 屏幕截图显示通过、失败的测试数量、持续时间等:

[![](img/915e726803f8aa955f58f5283c2f82a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6viQGJh9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2732/0%2AUhgWz-6aymdYyeMt.png)

仪表板访问概述:

[![](img/a417c2476eefc1f1a8bd319183041d2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m_0g_Zbc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2732/0%2AnwKEpSx6YRqggszI.png)

在这里，我选择在 Selenium 中使用 TestNG 同时通过两个浏览器进行并行测试，您甚至可以选择同时使用多个浏览器。

整个想法的主要本质是通过更好的测试覆盖来节省时间和精力，Selenium 中的并行测试有助于我们实现这一点，并且在我们不时推出多个版本的浏览器时，这可能是当前急需的需求

随着用户市场的增长和人们对那些无止境的设备的接触，你的应用程序对所有那些众多平台和浏览器的兼容性在尽可能短的时间内对我们测试人员来说变得至关重要。在[测试自动化](https://www.lambdatest.com/selenium-automation?utm_source=hashnode&utm_medium=organic&utm_campaign=sep08_sd&utm_term=sd&utm_content=webpage)的帮助下，选择这些基于云的技术可能是我们可以选择的最有成效的途径。

观看此视频，了解如何设置和使用 TestNG with Selenium 来自动化您的测试过程。我们还将介绍 TestNG Priority 方法，它允许您编写易于阅读和维护的测试。

[https://www.youtube.com/embed/X3HdVgjafpA](https://www.youtube.com/embed/X3HdVgjafpA)

[![](img/5240ebe3d6180f61eae82e26491a8987.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7N-VKOJB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AY2SDVya6em_WALsy.png)