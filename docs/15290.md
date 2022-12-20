# 审查您的测试自动化代码

> 原文：<https://dev.to/juperala/review-your-test-automation-code-271a>

做测试自动化就是写代码。测试自动化代码很容易被视为二等公民。由于没有交付给客户，开发通常不太正式，并且可能缺乏组织中应用的审查和质量实践。许多初级开发人员和测试人员也经常是从第一个代码开始他们的编程之旅。

然而，当谈到创建有价值和可维护的自动化时，自动化代码应该在考虑可维护性的情况下很好地实现。测试应该是高度可靠的，因为任何假阳性、假阴性或测试碎片都会破坏对自动化结果的信任。

提高自动化质量的一个方法是应用代码评审实践，其中自动化测试由您的同行评审。通过代码审查实践，您可以识别自动化中可能影响测试结果和稳定性的问题。

这篇文章列出了自动化代码中可能存在的问题，以便在代码评审中查找。

# 代码评审中需要注意的事情

## 这只是代码

如前所述，测试自动化只是代码。因此，在评审过程中可以应用通用的代码评审指南。如果不熟悉评审，请查看您的组织评审指南和实践。例如，要寻找的常见事物:

*   代码可以理解吗？有没有可以简化的不必要的复杂性？
*   函数或类太大了吗，可以分解成更小的部分吗？
*   代码单元的职责是否过多？单一责任原则？
*   代码遵循干巴巴的(不要重复自己)模式吗？代码中是否存在重复？
*   代码是否遵循通用的编码标准和约定？

## 特定于测试代码的问题

除了要寻找的一般代码评审问题之外，测试逻辑中还有许多问题区域和问题可以在代码评审中识别出来。接下来的章节描述了几个常见的需要注意的类别。

使用 Selenium WebDriver 和 Java 的代码示例演示了这些问题，但是所呈现的问题类别本身并不特定于任何特定的语言或自动化框架。

### 坏断言

测试脚本中常见的一个错误是忘记断言。缺乏断言会使你的测试产生假阳性的测试结果。

```
 private void submitIsDisplayed() {
        WebElement button = driver.findElement(By.id("submit"));
        // check that element is displayed
        button.isDisplayed();  // MISSING ASSERTION
    } 
```

### Retrys

另一个常见的问题是试图通过在异常情况下重试测试步骤来解决测试稳定性问题。通常这是脆弱的自动化代码和缺乏对应用程序内部行为的理解的标志。确定导致测试步骤失败的应用程序行为，并创建所需的等待或缺失测试逻辑来可靠地处理它。

```
 private void inputAmount(String amount) {
        try {
            WebElement field = driver.findElement(By.id("amount"));
            field.sendKeys(amount);
        } catch(StaleElementReferenceException e) {
            // INPUT FAILED, RETRYING
            WebElement field = driver.findElement(By.id("amount"));
            field.sendKeys(amount);
        }
    } 
```

### 休眠和固定延迟

与上述重试非常相似的是固定延迟(休眠),通常用于修复测试稳定性问题。确定导致测试步骤失败的应用程序事件，并为其创建适当的等待。不要用固定的延迟来减缓测试。

```
 private void acceptCookie() throws InterruptedException {
        // FIXED DELAY
        Thread.sleep(5000);
        WebElement cookieButton = driver.findElement(By.id("accept"));
        cookieButton.click();
    } 
```

### 硬编码值和配置

有一点是肯定的，你永远不知道你的测试将来会在哪里运行。因此，自动化代码应该易于配置。如果需要在不同的环境下测试呢？使用不同的用户？还是用不同的浏览器？通过避免固定的 URL、驱动程序等，从一开始就使你的测试可配置。

```
 private void init() {
        driver = new FirefoxDriver(); // FIXED BROWSER TYPE
        driver.get("https://192.168.1.5/app"); // FIXED URL
    } 
```

### 错误的抽象层次

基于 BDD 的自动化常见的问题是错误的抽象层次。在 BDD 中，故事文件和场景应该作为描述你的产品行为的例子，而不需要详细的实现机制——允许不同的利益相关者对产品行为有共同的理解(和交流的方式)。

特定于实现的场景通常也表明 BDD 仅仅是为了自动化的目的而创建的，而不是作为整个开发过程的公共文档。因此，在自动化中拥有 BDD 层仅仅是一个额外的样板文件，没有真正的价值。

```
Scenario: Application can be started
Given application is opened
When user clicks element "//button[@title='Start']" # SPECIFICS TO UI IMPLEMENTATION
Then element "#app > div.started" is visible        # SPECIFICS TO UI IMPLEMENTATION 
```

### 缺乏原子性

另一件要注意的事情是连锁测试，它们不能相互独立运行。链式测试使得理解和调试测试变得更加复杂，并且还会导致故障传播。单一测试的失败也会导致所有后续测试的失败。

```
@FixMethodOrder(MethodSorters.NAME_ASCENDING)
public class ChainedTests {

    // ... init ....

    @Test
    public void t1_canLoadPage() {
        driver.get(getAppUrl());
        wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("password")));
    }

    // DEPENDENT ON T1_canLoadPage
    @Test
    public void t2_canLogin() {
        driver.findElement(By.id("userid")).sendKeys(userId);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.id("login")).click();
        wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("order")));
    }

    // DEPENDENT ON T1_canLoadPage and T2_canLogin
    @Test
    public void t3_canPlaceAnOrder() {
        driver.findElement(By.id("order")).click();
        wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("confirmation")));
    }
} 
```

### 不可靠的选择器

基于 UI 的测试通常使用某种选择器来识别要交互的元素。使用不可靠和模糊的选择器会使您的测试变得脆弱，甚至在测试范围内出现微小的 UI 变化时也会导致测试失败。例如，当额外的标记作为特定元素的父元素或兄弟元素添加到 UI 中时，使用绝对 xpath 或 css locator 可能会中断。

根据需要在产品中添加唯一的定位器以支持测试。如果这是不可能的，确定稳定的方法来定位元素。避免使用绝对 css 或 xpath 定位符，因为它们很容易被破坏。

```
 private String getDisclaimer() {
        // ABSOLUTE XPATH LOCATOR THAT GETS EASILY BROKEN
        By disclaimer = By.xpath("html/body/div[1]/section/div[1]/div/div/div/div[1]/div/div/div/div/div[3]/div[1]/div/h4");
        return driver.findElement(disclaimer).getText();
    } 
```

# 学习机会

代码审查不应该仅仅被看作是识别代码中问题的一种手段。阅读他人的代码并获得自己的反馈是提高自动化技能的一个好方法。总是有很多东西要互相学习。

# 意为结束

列出的列表旨在识别可能导致您的测试脆弱、错误或难以维护的问题。然而，它并不旨在成为被认为是不良实践的终极真理。在某些情况下，使用所介绍的方法可能是适用的，甚至是创建工作区以启动和运行测试的唯一方法。有时也可以通过编写快速测试来验证一次。意味着结束。

*这是转贴自[jperala . fi](https://jperala.fi)T3】的一篇原创文章*