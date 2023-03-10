# Chrome Headless 简介

> 原文：<https://dev.to/scrapingbee/introduction-to-chrome-headless-469b>

在之前的文章中，我向您介绍了两种不同的工具来使用 Java 执行 web 抓取。 [HtmlUnit](https://dev.to/scrapingbee/introduction-to-web-scraping-with-java-5i8) 在第一篇，还有 [PhantomJS](https://dev.to/scrapingbee/web-scraping-handling-ajax-website-1ip8) 在关于处理 Javascript 重网站的文章。

这一次我们要介绍 Chrome 的一个新功能，即 ***无头*** 模式。有传言说谷歌使用了一个特殊版本的 Chrome 来满足他们的抓取需求。我不知道这是不是真的，但谷歌在几个月前就用 Chrome 59 推出了 Chrome 的无头模式。

PhantomJS 是这个领域的领导者，它过去(现在仍然)大量用于浏览器自动化和测试。在听到关于 Headless Chrome 的消息后，PhantomJS 的维护者说他将不再担任维护者，因为我引用*“Google Chrome 比 PhantomJS 更快更稳定[...]"*
看起来 Chrome headless 正在成为浏览器自动化和处理大量 Javascript 网站的发展方向。

HtmlUnit、PhantomJS 和其他无头浏览器是非常有用的工具，问题是它们不如 Chrome 稳定，有时你会遇到 Chrome 不会出现的 Javascript 错误。

### 先决条件

*   谷歌浏览器> 59
*   [Chromedriver](https://sites.google.com/a/chromium.org/chromedriver/downloads)
*   硒
*   在你的 ***pom.xml*** 中添加一个最新版本的 Selenium:

```
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>3.8.1</version>
</dependency> 
```

如果你没有安装谷歌 Chrome，你可以在这里下载
安装 Chromedriver 你可以在 MacOS 上使用 brew:

```
brew install chromedriver 
```

或者使用下面的链接下载。
版本很多，建议你用 Chrome 和 chromedriver 的最后一个版本。

### 让我们登录黑客新闻

这部分我们要登录黑客新闻，一旦登录就截图。对于这个任务，我们不需要 Chrome headless，但是本文的目标只是向您展示如何使用 Selenium 运行 headless Chrome。

我们首先要做的是创建一个 WebDriver 对象，并设置 chromedriver 路径和一些参数:

```
// Init chromedriver
String chromeDriverPath = "/Path/To/Chromedriver" ;
System.setProperty("webdriver.chrome.driver", chromeDriverPath);
ChromeOptions options = new ChromeOptions();
options.addArguments("--headless", "--disable-gpu", "--window-size=1920,1200","--ignore-certificate-errors");
WebDriver driver = new ChromeDriver(options); 
```

```
 option is needed on Windows systems, according to the [documentation](https://developers.google.com/web/updates/2017/04/headless-chrome)
Chromedriver should automatically find the Google Chrome executable path, if you have a special installation, or if you want to use a different version of Chrome, you can do it with :

```java
options.setBinary("/Path/to/specific/version/of/Google Chrome"); 
```

如果你想了解更多不同的选项，这里是 Chromedriver 文档

下一步是对 Hacker News 登录表单执行 GET 请求，选择用户名和密码字段，用我们的凭据填充它，然后单击登录按钮。然后我们必须检查凭证错误，如果我们登录了，我们可以截图。

[![](img/eda6a8826bf058ab642aaf484175f950.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ax81Lgo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.scrapingbee.cimg/post/java-headless/hn_screenshot.png)

我们在以前的文章中已经这样做了，下面是完整的代码:

```
public class ChromeHeadlessTest {
    private static String userName = "" ;
    private static String password = "" ;

    public static void main(String[] args) throws IOException{
       String chromeDriverPath = "/your/chromedriver/path" ;
       System.setProperty("webdriver.chrome.driver", chromeDriverPath);
       ChromeOptions options = new ChromeOptions();
       options.addArguments("--headless", "--disable-gpu", "--window-size=1920,1200","--ignore-certificate-errors", "--silent");
       WebDriver driver = new ChromeDriver(options);

      // Get the login page
      driver.get("https://news.ycombinator.com/login?goto=news");

      // Search for username / password input and fill the inputs
      driver.findElement(By.xpath("//input[@name='acct']")).sendKeys(userName);
      driver.findElement(By.xpath("//input[@type='password']")).sendKeys(password);

      // Locate the login button and click on it
      driver.findElement(By.xpath("//input[@value='login']")).click();

      if(driver.getCurrentUrl().equals("https://news.ycombinator.com/login")){
        System.out.println("Incorrect credentials");
        driver.quit();
        System.exit(1);
      }else{
        System.out.println("Successfuly logged in");
      }

        // Take a screenshot of the current page
        File screenshot = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
        FileUtils.copyFile(screenshot, new File("screenshot.png"));

        // Logout
        driver.findElement(By.id("logout")).click();
    driver.quit();
   }
} 
```

你现在应该有一个很好的黑客新闻主页的截图了。如你所见，Chrome headless 真的很容易使用，它和 PhantomJS 没有什么不同，因为我们使用 Selenium 来运行它。

如果你喜欢这个，不要犹豫，订阅我们的时事通讯！

如果你喜欢网络抓取并且厌倦了代理、JS 渲染和验证码，你可以检查我们新的[网络抓取 API](https://www.scrapingbee.com) ，第一批 1000 个 API 调用由我们负责。

像往常一样，代码可以在这个 [Github 库](https://github.com/ksahin/introWebScraping)中获得