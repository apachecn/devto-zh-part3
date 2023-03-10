# Selenium 测试的页面对象模式入门

> 原文：<https://dev.to/kimschiller/getting-started-with-page-object-pattern-for-your-selenium-tests-3ghe>

## 简介

和大多数软件模式一样，一旦你实际编码到你意识到你需要帮助的情况，它们才是最有意义的。预先学习一个模式可以防止你犯设计错误，但是在大多数情况下，犯错误是你学习的最好机会。

然而，无论您是已经有了一套功能性的 Selenium 测试，还是刚刚开始，Page 对象模式都非常有意义。

这里的代码示例是用 Java 编写的，但是这些概念适用于任何语言。你可以在 github 上找到完整的[代码示例。](https://github.com/kschiller/page-object-pattern-tutorial)

## 问题

当您使用 Selenium 编写功能测试时，您的代码的主要部分将包括通过 WebDriver API 与您正在测试的 web 接口的交互。获取元素后，您将通过各种断言验证元素的某些状态，然后继续获取下一个元素。

将此示例视为基本硒测试的一部分:

```
List<WebElement> zipCodes = driver.findElements(By.id("zipCodes"));
for (WebElement zipCode : zipCodes) {
    if (zipCode.getText().equals("12345")){
        zipCode.click();
        break;
    }
}
WebElement city = driver.findElement(By.id("city"));
assertEquals("MyCityName", city.getText()); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的测试示例，它获取并迭代一个查找 12345 的邮政编码列表，单击它并获取城市元素，期望城市名称为 MyCityName。

即使像这样简单的测试，可读性也很差。有大量的 WebDriver 代码，模糊了测试的目的，使测试变得缓慢和难以理解。

对于任何界面，尤其是 web 界面，经常会对 UI 进行小的和大的修改。这可能是一个新的设计，字段和按钮的重组，这可能会影响你的测试。所以你的测试失败了，你需要更新你的选择器。

现在，如果你对一个使用“快乐路径”的页面只有一个功能测试，你可能不会认为这是一件大事。然而，如果你有一整套回归测试，这绝对是一件大事。

因此，这种硒测试的一些典型问题是:

*   测试用例很难阅读
*   UI 的变化通常会在几个地方破坏多个测试
*   测试内部和测试之间的选择器重复——没有重用

## 解

因此，页面对象模式引入了基本上是一个解耦层，而不是让每个测试直接获取元素并容易受到 UI 变化的影响。

您可以创建一个对象来表示您想要测试的 UI，它可以是整个页面，也可以是页面的重要部分。这个对象的职责是包装 HTML 元素并封装与 UI 的交互，这意味着所有对 WebDriver 的调用都将在这里进行。这是大多数 WebElements 所在的位置。而且这是 UI 更改时唯一需要修改的地方。

下图说明了这种模式:

<figure>[![Page Object Pattern](img/12874597fabaabe4c67b205926f11dde.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G8_xgI12--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kimschiller.com/page-object-pattern-tutorial/1.png) 

<figcaption>图 1:页面对象模式</figcaption>

</figure>

## 实现

实际上，开始实施非常简单:

1.  基本硒设置
2.  分析测试中的应用程序
3.  创建页面对象
4.  编写测试

### 1。基本硒设置

当您启动 Selenium 测试套件时，我建议创建一个类来保存所有驱动程序生命周期管理代码。所以从这样一个类开始:

```
public class FunctionalTest {
    protected static WebDriver driver;

    @BeforeClass
    public static void setUp(){
        driver = new FirefoxDriver();
        driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
    }

    @After
    public void cleanUp(){
        driver.manage().deleteAllCookies();
    }

    @AfterClass
    public static void tearDown(){
        driver.close();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[https://github . com/ks chiller/page-object-pattern-tutorial/blob/Initial/functional test . Java](https://github.com/kschiller/page-object-pattern-tutorial/blob/Initial/FunctionalTest.java)

现在，使它成为您创建的任何 testclass 的超类。

### 2。分析测试中的应用程序

对于这个例子，我已经创建了一个带有姓名和地址字段的注册表单，以及一个收据页面。这些例子可以在 https://www.kimschiller.com/page-object-pattern-tutorial 的现场直播中看到。

<figure>[![Signup form](img/1531193802b781624dc9f603248445da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uetvKOqi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kimschiller.com/page-object-pattern-tutorial/2.png) 

<figcaption>图 2:报名表格</figcaption>

</figure>

在这个例子中，分析非常简单，我将为每个页面创建一个 page 对象。但是如上所述，在一个完整的 HTML 页面和一个页面对象之间不一定存在一对一的映射。页面对象应该代表页面中有意义的上下文部分。

### 3。创建页面对象

现在我将创建两个页面对象，一个用于注册表单，一个用于收据。

```
public class SignUpPage extends PageObject {

    @FindBy(id="firstname")
    private WebElement firstName;

    @FindBy(id="lastname")
    private WebElement lastName;

    @FindBy(id="address")
    private WebElement address;

    @FindBy(id="zipcode")
    private WebElement zipCode;

    @FindBy(id="signup")
    private WebElement submitButton;

    public SignUpPage(WebDriver driver) {
        super(driver);
    }

    public void enterName(String firstName, String lastName){
        this.firstName.clear();
        this.firstName.sendKeys(firstName);

        this.lastName.clear();
        this.lastName.sendKeys(lastName);
    }

    public void enterAddress(String address, String zipCode){
        this.address.clear();
        this.address.sendKeys(address);

        this.zipCode.clear();
        this.zipCode.sendKeys(zipCode);
    }

    public ReceiptPage submit(){
        submitButton.click();
        return new ReceiptPage(driver);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[https://github . com/ks chiller/page-object-pattern-tutorial/blob/Initial/sign up page . Java](https://github.com/kschiller/page-object-pattern-tutorial/blob/Initial/SignUpPage.java)

```
public class ReceiptPage extends PageObject {

    @FindBy(tagName = "h1")
    private WebElement header;

    public ReceiptPage(WebDriver driver) {
        super(driver);
    }

    public String confirmationHeader(){
        return header.getText();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[https://github . com/ks chiller/page-object-pattern-tutorial/blob/Initial/receipt page . Java](https://github.com/kschiller/page-object-pattern-tutorial/blob/Initial/ReceiptPage.java)

现在，这实际上将不起作用，直到我们初始化我们已经注释的 WebElements。所以我将创建另一个超类来保存这段小而重要的代码。所以我的 PageObject 类看起来像这样:

```
public class PageObject {
    protected WebDriver driver;

    public PageObject(WebDriver driver){
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[https://github . com/ks chiller/page-object-pattern-tutorial/blob/Initial/page object . Java](https://github.com/kschiller/page-object-pattern-tutorial/blob/Initial/PageObject.java)

PageFactory 处理所有带注释的 WebElements，并使用带注释的选择器在页面上定位元素。你可以在 SeleniumHQ GitHub 页面上找到 PageFactory 的文档。

### 4。编写测试

所以现在您已经拥有了开始编写实际测试用例的所有部分。下面是一个通过成功注册和确认的测试案例:

```
public class SignUpFormTest extends FunctionalTest {

    @Test
    public void signUp(){
        driver.get("http://www.kimschiller.com/page-object-pattern-tutorial/index.html");

        SignUpPage signUpPage = new SignUpPage(driver);
        assertTrue(signUpPage.isInitialized());

        signUpPage.enterName("First", "Last");
        signUpPage.enterAddress("123 Street", "12345");

        ReceiptPage receiptPage = signUpPage.submit();
        assertTrue(receiptPage.isInitialized());

        assertEquals("Thank you", receiptPage.confirmationHeader());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[https://github . com/ks chiller/page-object-pattern-tutorial/blob/Initial/sign up formtest . Java](https://github.com/kschiller/page-object-pattern-tutorial/blob/Initial/SignUpFormTest.java)

现在看看测试用例，我认为我们可读性差的问题已经解决了。测试读起来很好，每个方法的意图都非常清楚。

因为我们已经从测试中移除了所有的 WebDriver 调用，所以如果 firstname-field 改变了它的 id，我们实际上并不需要改变测试。只有页面对象需要更改。

## 最佳实践

有一些使用页面对象的最佳实践，您应该努力遵循。

*   页面对象不应该有任何断言
*   页面对象应该代表页面中有意义的元素，而不一定是完整的页面
*   当你导航时，你应该返回下一页的页面对象

## 提示&诡计

在与页面交互之前验证页面是否已经准备好是一个很好的实践，正如我正在做的 SignUpFormTest 所示:

```
SignUpPage signUpPage = new SignUpPage(driver);
assertTrue(signUpPage.isInitialized()); 
```

Enter fullscreen mode Exit fullscreen mode

您可能会在 page 对象的每个实例中重复这种模式，所以这是我们可以引入一个例外的地方，即在我们的 page 对象中没有断言的经验法则。

这里的想法是将这个断言移动到 page 对象的构造函数中。如果页面对象由于某种原因没有及时准备好，那么创建将会失败。

所以你的构造函数应该是这样的:

```
public SignUpPage(WebDriver driver) {
    super(driver);
    assertTrue(firstName.isDisplayed());
} 
```

Enter fullscreen mode Exit fullscreen mode

很好很容易。

## 结论

o 无论您是刚刚开始使用 Selenium，还是正在管理一个大型的 Selenium 回归测试套件，将页面对象模式引入到您的代码中，您都将受益匪浅。作为 WebDriver 的创造者，Simon Stewart - [@shs96c](https://twitter.com/shs96c) 直言不讳地说:“如果你的测试方法中有 WebDriver APIs，那你就做错了”。而我个人一直做错了，也经历了做这件事的挫败感。

正如我们现在所看到的，页面对象模式通过引入一系列页面对象，为您提供了一种将测试脚本与您正在测试的 web 界面分离的方法。和页面对象负责与您正在测试的网页进行通信。通过 WebDriver API 触发的任何 DOM 查询都要经过 page 对象，因为只有 page 对象应该知道如何使用 Selenium 提供的各种定位器方法在页面上找到元素。

通过使用页面对象，你的测试变得更加简洁和易读。您的要素定位器是集中式的，这使得维护更加容易。用户界面的变化只影响页面对象，不影响测试脚本。最后，这是一个好的、可靠的面向对象设计。