# 在 Selenium Webdriver 中编写第一个脚本:Java 代码示例

> 原文：<https://dev.to/jessicaschuller/write-the-first-script-in-selenium-webdriver-java-code-example-4nal>

在这篇博客中，您将有机会习惯 Selenium 自动化测试，以及作为基础水平的如何在 Selenium WebDriver 中编写脚本的完整说明。本教程还提供了一个实际的 Java 代码作为 **Selenium WebDriver 示例**。

## 观众

该教程为自动化测试人员提供了**如何在 Selenium Webdriver** 中编写脚本的基础知识和实际例子。这篇文章包含了足够的内容，让您从 Selenium WebDriver 开始，从这里您可以独立地将自己带到更高的专业水平。

## 先决条件

在开始学习本教程之前，您需要具备 Java 或任何其他面向对象编码语言的基础知识。而且你要精通测试概念的基本原理，尤其是 **Selenium 自动化测试**的概念。

## Selenium WebDriver 脚本入门

假设您想要**在 Selenium Webdriver** 中编写脚本，该脚本可以:

*   获取福克斯新闻频道的主页
*   验证其标题
*   打印出比较结果
*   在结束整个程序之前关闭它

## web 驱动程序代码

下面是上面提到的场景呈现的逻辑的 [**Selenium WebDriver 示例**](https://seleniumtestingtutorials.com/selenium-webdriver/write-script-selenium-webdriver-java//) 。

注意:使用 WebDriver 时要考虑 Mozilla 生成的 Gecko 驱动。Selenium 3.0、Firefox 和 gecko 存在兼容性问题，适当地设置它们可能会成为一项艰巨的任务。如果代码无法激活，应该下载 Firefox 的较低版本。否则，您可以在 Chrome 上运行 Selenium 脚本。你只需要修改 3 行代码就可以让你的脚本在 Firefox 或者 Chrome 上运行。

```
package newproject;

import org.openqa.selenium.WebDriver;

import org.openqa.selenium.firefox.FirefoxDriver;

//comment the above line and uncomment below line to use Chrome

//import org.openqa.selenium.chrome.ChromeDriver;

public class PG1 {

public static void main(String[] args) {

// declaration and instantiation of objects/variables

System.setProperty("webdriver.firefox.marionette","C:\\geckodriver.exe");

WebDriver driver = new FirefoxDriver();

//comment the above 2 lines and uncomment below 2 lines to use Chrome

//System.setProperty("webdriver.chrome.driver","G:\\chromedriver.exe");

//WebDriver driver = new ChromeDriver();

String baseUrl = "http://www.foxnews.com/";

String expectedTitle = "Fox News - Breaking News Updates | Latest News Headlines | Photos & News Videos";

String actualTitle = "";

// launch Fire fox and direct it to the Base URL

driver.get(baseUrl);

// get the actual value of the title

actualTitle = driver.getTitle();

/*

* compare the actual title of the page with the expected one and print

* the result as "Passed" or "Failed"

*/

if (actualTitle.contentEquals(expectedTitle)){

System.out.println("Test Passed!");

} else {

System.out.println("Test Failed");

}

//close Fire fox

driver.close();

}

}
```

## 代码的解释

### 导入包/语句

首先，你需要导入以下两个包准备好:

1.  **org.openqa.selenium.*:** 引用实例化新 web 浏览器所需的 WebDriver 接口。
2.  **org . open QA . selenium . firefox . FirefoxDriver**:引用在 WebDriver 类操作的浏览器上实例化 Firefox 特定驱动程序所需的 Firefox driver 类。

如果您的测试需要更复杂的任务，比如捕获浏览器屏幕、访问另一个类或者操作外部文件，那么您肯定需要导入更多的包。

### 实例化对象和变量

通常，驱动程序对象是这样实例化的:

```
WebDriver driver - new FirefoxDriver();
```

基本 URL 和预期标题保存为变量。

### 启动浏览器会话

WebDriver **get()** 方法有助于启动一个新的浏览器会话，并将其直接传送到您指定为其参数的 URL。

```
driver.get(baseUrl);
```

### 获取实际的页面标题

可以通过使用 WebDriver 接口的 **getTitle()** 方法来验证当前加载页面的页面标题。

```
actualTitle = driver.getTitle();
```

### 比较实际值和期望值

这部分代码简单地应用了一个基本的 Java if-else 格式来比较实际标题和预期标题。

```
 if (actualTitle.contentEquals(expectedTitle)){
System.out.println("Test Passed!");

} else {

System.out.println("Test Failed");

}
```

### 结束浏览器会话

方法帮助你关闭浏览器窗口。

```
  driver.close();
```

### 结束整个程序

如果您使用此命令而没有先关闭所有浏览器窗口，那么您的整个 Java 程序将会在浏览器窗口仍然打开的情况下结束。

```
system.exist(0)
```

## 执行测试

有两种方法可以在 Eclipse 中运行代码。

1.  在 Eclipse 菜单栏上，选择**运行** >运行 T2。
2.  按下 **Ctrl+F11** 运行整个代码。

[![Run on Eclipse Menu Bar](img/12d018497986216a53038f31a64b7202.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8t3xRojD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://seleniumtestingtutorials.com/wp-content/uploads/2018/10/Selenium-WebDriver-1-1.png)

如果您正确地完成了所有事情，Eclipse 将输出“测试通过！”

[![Test Passed](img/883506e21da56fc13fa251aef20d93f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Axhavvql--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://seleniumtestingtutorials.com/wp-content/uploads/2018/10/Selenium-WebDriver-2-1.png)

## 定位 Web 元素

我们使用动态查找器**find element(by . locator())**来定位 WebDriver 中的元素。

下面是一个通过 id 定位元素的示例代码。让我们以脸书为例的基础网址。

| 

```
package newproject;

import org.openqa.selenium.By;

import org.openqa.selenium.WebDriver;

import org.openqa.selenium.firefox.FirefoxDriver;

public class PG2 {

   public static void main(String[] args) {

    System.setProperty("webdriver.firefox.marionette","C:\\geckodriver.exe");

    WebDriver driver = new FirefoxDriver();

       String baseUrl = "http://www.facebook.com";

       String tagName = "";

       driver.get(baseUrl);

       tagName = driver.findElement(By.id("email")).getTagName();

       System.out.println(tagName);

       driver.close();

       System.exit(0);

}

}
```

 |

我们可以使用 **getTagName()** 方法提取 id 名为“email”的特定元素的标记名。当被执行时，这段代码应该能够准确地识别标签名“input ”,并将提取到 Eclipse 控制台窗口。

[![Console](img/1a976d80fab9e9f7d25a3e51716b61ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hW1vf9to--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://seleniumtestingtutorials.com/wp-content/uploads/2018/10/Selenium-WebDriver-3-1.png)

## 定位器类型及其语法

通过“id”属性的值定位通过“class”属性的值定位通过超链接的文本值定位通过超链接的子文本值定位通过“name”属性的值定位通过 XPath 的值定位通过 CSS 选择器的值定位

| 定位器类型 | 语法 | 描述 |
| id | driver . find ElementT2(by . ID(" ID _ of _ Element ")) | 通过 的值定位“id”属性 |
| 类名称 | driver . find Element(by . Class name(“元素类”)) | 通过 的值定位“类”属性 |
| 链接文本 | driver . find element(by . linktext(" Text ")) | 通过超链接的 文本的值定位 |
| 部分链接文本 | driver . find element(by . partiallinktext(“PartialText”) | 根据超链接的 子文本的值定位 |
| 名称 | driver . find Element(by . Name(“元素名称”)) | 通过 【名称】属性的值定位 |
| xpath | driver . find element(by . Xpath(" Xpath ")) | 通过 xpath 的值 定位 |
| cssSelector | driver . find element(by . CSS Selector(“CSS 选择器”) | 通过 的值定位 CSS 选择器 |
| 标记名 | driver . find elementT2】(by . tagname(" input ")) | 通过 的值定位其标签名 |

## 结论

本教程为您提供了 [**如何在 Selenium WebDriver**](https://seleniumtestingtutorials.com/selenium-webdriver/write-script-selenium-webdriver-java/) 中编写脚本的基本说明。该博客还讨论了构成 Selenium WebDriver 脚本的不同元素。

下面是 Selenium WebDriver 教程的摘要:

*   在编写脚本之前，您需要导入允许您创建 WebDriver 脚本的包。
*   **get()** 方法有助于启动一个纯 web 浏览器实例。
*   **getTitle()** 方法检索页面标题，并将检索到的标题分配给一个字符串对象。
*   在 WebDriver 中，我们可以使用动态查找器来定位 web 元素。
*   以下是可用的定位器类型:
    *   身份
    *   className
    *   名字
    *   XPath
    *   css 选择器
    *   linkText
    *   部分链接文本
    *   tagName