# 使用量角器和 Selenium 进行自动跨浏览器测试

> 原文：<https://dev.to/lambdatest/automated-cross-browser-testing-with-protractor--selenium-411i>

我们生活在一个市场上有大量测试自动化工具或库的时代。这种丰富带来了困惑，选择正确的框架变得非常具有挑战性。尽早确定合适的工具对于获得更大的投资回报至关重要。对于基于角度 JS 的应用程序，我们不必太担心，因为我们有量角器。Google 强烈推荐量角器为 AngularJS 提供端到端的自动化，因为两者都是由 Google 维护和拥有的，它们构建了 JavaScript 测试自动化框架，以更好的方式处理 AngularJS 组件。在这篇文章中，我们将详细探讨 JavaScript 测试自动化框架的含义。量角器是什么？为什么量角器非常适合测试自动化？它如何帮助实现不同浏览器的跨浏览器测试自动化？

### 我们所说的 JavaScript 测试自动化框架是什么意思？

在过去的几年里，JavaScript 框架的使用有了很大的增长，原因是，现在大多数应用程序都是使用 Angular JS 或 React JS 这样的技术开发的。事实上，最初， [AngularJS 是 web 开发者的首选](https://www.lambdatest.com/blog/angularjs-a-developers-first-choice/)，然而，到 2018 年底，我们可以看到 ReactJS 在 web 上的主要优势。 [ReactJS 甚至可能继续主宰 2019 年](https://www.lambdatest.com/blog/will-reactjs-continue-to-dominate-in-2019/)。为了解决测试自动化中的这些问题，我们应该采用正确的编程语言和测试自动化框架。

JavaScript 测试自动化框架是端到端的框架，专门为 web 应用程序运行自动化脚本。然而，根据以下选择标准，选择合适的产品可能非常具有挑战性。

*   [端到端测试](https://www.lambdatest.com/blog/all-you-need-to-know-about-end-to-end-testing/)
*   断言库
*   BDD/TDD 集成
*   整合测试
*   来自社区的定期更新
*   [跨浏览器布局测试](https://www.lambdatest.com/blog/using-galen-framework-for-automated-cross-browser-layout-testing/)
*   [视觉回归测试](https://www.lambdatest.com/smart-visual-ui-testing)

### 与其他语言相比，为什么 JavaScript 框架是必要的？

JavaScript 对 web 开发人员来说并不新鲜。组织中的大多数开发人员编写 JavaScript 代码来开发 web 应用程序、单元测试等。以下是考虑 JavaScript framework pivotal 的原因。

*   易于遵循的[左移测试，以提高产品质量](https://www.lambdatest.com/blog/how-shift-left-testing-can-help-your-product-quality/)。
*   为开发人员提供了生成端到端测试的灵活性，以及更快、更可靠交付的单元测试。
*   开发人员和质量保证人员使用同一种语言进行更多的合作，以实现测试自动化。
*   帮助实现测试自动化方面的开发 QA 测试和结对编程。
*   更灵活地使用相同的语言工作，生产率更高。
*   使用相同语言的正确工具和正确方法可以获得更大的收益。

### 什么是量角器？

量角器是一个 [JavaScript 框架](https://www.lambdatest.com/blog/top-javascript-frameworks-for-2019/)，用于 Angular 和 AngularJS 应用的端到端测试自动化框架。它在真实浏览器中测试您的应用程序，就像真实用户一样。

量角器建立在 WebDriver JS (Selenium)之上，除了默认定位器之外，它还提供了特定角度的定位器策略。它不仅仅是为了测试 AngularJS 应用程序，也有助于为非 Angular JS 应用程序编写自动化测试。

### 什么使得量角器非常适合你的自动化测试脚本？

量角器是一个开源的端到端测试自动化框架，由 Google 提出，这使得它成为一个可靠的、著名的和最新的框架。量角器是一个 Node.js 程序，支持 Jasmine、Mocha 和 Cucumber 等测试框架。

**量角器的主要特点:**

*   量角器是围绕 Selenium WebDriver JS 和 Selenium Server 开发的包装器。与 Selenium 相比，Selenium WebDriver JS 所有关键特性和附加实现有助于实现更好的自动化支持和更少的维护。
*   量角器在本地定位器的基础上提供了新的定位器策略，这实际上有助于在 DOM 中轻松定位元素。
*   *按模型、按绑定、按中继器等。,
*   量角器支持页面对象设计模式——有助于编写清晰易读的代码。对于自动化测试人员来说，编写干净的代码是必须的。这里有 [8 个可行的见解，用于编写更好的自动化代码](https://www.lambdatest.com/blog/8-actionable-insights-to-write-better-automation-code/)。
*   量角器利用 Selenium 网格来初始化、控制和运行浏览器实例。
*   可以很容易地集成 jasmine、mocha 和 cumber 框架来编写您的测试。

**量角器结合硒的优势:**

*   与其他工具或库相比，速度非常快。
*   支持通过多个浏览器进行跨浏览器并行测试。
*   不需要工作在同步策略上，例如，等待像隐式，显式和睡眠等。，量角器具有与 Angular JS 框架连接的内置功能和智能，并知道 Angular 何时完成 DOM 渲染，例如，页面加载等待，同步，
*   特定角度定位器支持。

### 使用量角器和 Selenium 开始跨浏览器自动化测试

以下是在 Windows 上使用量角器和 Selenium 执行跨浏览器自动化测试所需的先决条件:

*   节点. js
*   使用 npm 安装量角器
*   更新 webdriver manager 并启动服务器
*   Visual Studio 代码

### 用量角器和 Selenium 在 Windows 中设置自动化测试环境

本节详细介绍了如何设置环境，通过 Windows 使用量角器和 Selenium 执行端到端的跨浏览器自动化测试。
**第一步:安装 Node.js**
你需要为 Node.js 安装 npm

**什么是 npm？**
npm 是一个免费使用的 javascript 开发包管理器，可以帮助你下载所有的公共软件包

**什么是包管理器**
node . js 中的一个包包含了一个模块需要的一组文件。模块什么都不是，它是可以包含在项目中的 javascript 库。
要安装 Node.js，点击[这里](https://nodejs.org/en/)。

[![](img/5a1498cb1d82e2281b8b4656a018bae1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HX0wasrQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/unnamed-21.png)

下载完安装程序后，以管理员身份运行它。

单击 Node.js 设置窗口中的“下一步”按钮继续。
[![](img/64762e7eb1c9e0134e4c82d945c1ffe9.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--LwB6pNUG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image9-5.png)

单击接受许可协议的复选框，然后单击下一步按钮。

[![](img/f563dabdf99f9be180bb8dccaa131210.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--juW4AXgN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image20-1.png)

保留默认的 windows 位置或随意更改位置，然后单击“下一步”。
[![](img/0347c0c4a61a76dd6d14d3b1f3a4c676.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--t4-uC4nM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image11-4.png)

此后，点击下一步和安装按钮。等待几分钟，安装完成。

安装完成后，您会看到以下屏幕。
[![](img/4205ee8207f0ffe1bd3804f9d8abceac.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--olUhVY6C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image18-1.png)

验证 npm 是否已安装在您的计算机中。请提供以下命令:
`npm –version`

**步骤 2:使用 npm**
安装量角器下载一个包，打开命令提示符并提供以下命令

`npm install –g protractor`

安装命令用于下载软件包

-g 代表全局，这意味着任何命令都以全局模式运行。

下面的快照表明量角器安装成功。
[![](img/b32d377c86d269ab69dc0990257dcb5c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--394nVlrW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image12-3.png)

要验证安装的量角器版本，提供以下命令
`protractor –version`

**步骤 3:更新网络驱动管理器并启动服务器**

要更新 webdriver manager，请提供以下命令

`webdriver-manager update`

一旦提供上述命令，它将下载所有必要的依赖。

[![](img/cb0ebacf755788233cfab8641351f07a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P05PCe_H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image31.png)

WebDriver manager 是下载必要二进制文件的辅助工具。这将有助于启动和停止 selenium 服务器。

要启动 selenium 服务器，
`webdriver-manager start`

[![](img/526438d068cf855a623a0e70f38582ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oBaK0WW3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image28.png)

要验证 selenium 服务器是否在浏览器中运行，请使用以下 url

[http://localhost:4444/wd/hub](http://localhost:4444/wd/hub)

量角器测试将发送请求到这个服务器，并控制一个本地浏览器，你可以看到关于服务器的状态信息。因此，您可以稍后继续使用量角器和 Selenium 运行您的 JS 自动化脚本。

**第四步:安装 Visual Studio 代码**
**什么是 Visual Studio 代码？**

Visual Studio code 由微软开发，是 Windows、Mac 和 Linux 操作系统的源代码编辑器。它与 Eclipse 或 Intellij 等其他编程编辑器非常相似。这个工具是微软为 Windows、Linux 和 Mac OS 开发的。它支持多种编程语言，要了解更多关于 Visual Studio 代码的信息，请访问[这里](https://en.wikipedia.org/wiki/Visual_Studio_Code)。

双击 Visual Studio 安装程序，然后单击“下一步”按钮。

[![](img/dda1e324cab9d91ba6cf363d4383d2ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ibUP8L9z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image14-1.png)

在设置–Visual Studio 代码窗口中单击“我接受协议”

[![](img/e00a956dea3838e95250714b7a729efd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--md007WvA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image21-1.png)

保留默认安装位置(或)随意更改计算机中的不同路径，然后单击“下一步”按钮。

[![](img/4321510c6dfcaeef63ae2b5f283b5539.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GvnNXULu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image13-2.png)

继续点击“下一步”并检查桌面快捷方式，如果你想，完成安装。

安装完成后，将出现以下窗口。

[![](img/70d84cd7bb9e9ce8873cb5c1b908f056.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E2bzDP3i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image15-1.png)

点击"完成"按钮，最后 Visual Studio 代码编辑器打开。

[![](img/e6f6dc42c5a9864a8ced07a932e6c365.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kMey36Fp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image30.png)

点击你想安装的语言，我建议你安装 JavaScript 和 TypeScript 来使用量角器和 Selenium 执行自动化测试用例。一旦点击，它会提示您选择“确定”按钮，这将需要几秒钟来安装选定的语言。

### 用量角器和 Selenium 编写自动化测试的样本代码

现在，我们将看一下使用量角器和 Selenium 进行自动化测试的 Javascript 代码示例，并在本地实例中执行测试，这意味着执行在您的机器中安装的浏览器上运行的测试。

要运行端到端自动化的量角器测试，您必须需要两个文件:

*   规范
*   配置

Specs: Specs 是实际的测试套件，有超过个使用 Jasmine 框架用 Javascript 设计的测试用例。

**什么是茉莉框架？**
Jasmine 是一个针对 Javascript 的 BDD(行为驱动部署)测试框架。这个框架有助于你写出清晰易读的代码。要了解更多关于 Jasmine 框架的信息，请访问[这里](https://jasmine.github.io/)。

### 使用量角器和 Selenium WebDriver(本地)执行自动化测试的单个实例

下面是一个简单的示例配置文件，以及如何使用 Jasmine Framework 在量角器中编写基本代码。

配置文件:

```
exports.config = {
  seleniumAddress: 'http://localhost:4444/wd/hub',
  specs: ['lambdatest_spec.js'],
  multiCapabilities: [{
    'browserName': 'chrome',
  },
  {
    'browserName': 'firefox'
  }]
}
```

在上面的快照中显示了如何定义配置文件的语法，以使用量角器和 Selenium 执行浏览器自动化。

*   seleniumAddress:量角器使用 Selenium Grid 在指定的浏览器中运行您的测试。
*   specs: specs 表示您可以在一个或多个 spec 文件中提到的测试套件，以运行多个测试。
*   功能:这是您可以为您的测试配置进行功能设置的地方，例如，浏览器版本、平台等。，如果没有提供任何功能，那么默认情况下，它会在您的机器上使用 chrome 浏览器。

```
describe('To verify Angular and non Angular application test', () => {
    it('To verify page title of Lambdattest', () => {
        browser.get("https://lambdatest.com");
        element(by.css('p.home-btn > a.home-cta')).click();
        expect(browser.getTitle()).toEqual('Signup - LambdaTest App | Free Cross Browser Testing Tool');
    });
});
```

我们将会清楚地看到上面的代码说明了什么。

describe -> describe 是 Jasmine 中为测试套件编写语法的关键字。这是脚本文件中测试用例集合的定义。

it –>是测试用例满足特定条件的关键字。在 Jasmine 框架中的 describe 下可以添加多个" it"。

在上面的代码中，我们启动浏览器，导航到注册页面，看到匹配的标题并传递结果。

要运行您的测试:

在 Visual Studio 代码中:转到视图菜单->单击“终端”并提供以下命令
[![](img/28d915948376cad834a9679e71fc8e17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7hewwKcl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image6-3.png)

**语法:运行您的测试套件量角器，后跟配置文件名**
注意:在运行您的测试之前，请通过提供以下命令来运行 selenium Grid:

`webdriver-manager start`

[![](img/5abe11e85f6c4c5c906472720e243600.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iC3z3Sqp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image32.png)

执行上述测试后，观察到以下预期的故障。

[![](img/95efa2ffb70419d80c630c8b9078580f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fSzIHYuL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/unnamed-22.png)

上面的截图表明网站[https://lambdatest.com](https://lambdatest.com/)不是使用 AngularJS 组件开发的。这是实现量角器在启动浏览器时如何工作的最佳实时示例。正如在上面的“优点”主题中提到的，量角器基本上是在启动应用程序时尝试连接 Angular JS 框架。力矩量角器识别并意识到它不是 AngularJS 组件，它建议我们关闭等待角度。

为了解决这个问题:我们有一个方法叫做“browser . waitforangularenabled(false)；”

现在，方法被添加到脚本中，正如你在下面的截图中看到的，量角器现在支持非角度 js 应用程序。

```
describe('To verify Angular and non Angular application test', () => {
    it('To verify page title of Lambdattest', () => {
        browser.waitForAngularEnabled(false);
        browser.get("https://lambdatest.com");
        element(by.css('p.home-btn > a.home-cta')).click();
        expect(browser.getTitle()).toEqual('Signup - LambdaTest App | Free Cross Browser Testing Tool');
    });
});
```

执行测试后，测试成功通过。

[![](img/b19f73c9c2f8f543d403345144505dc6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6mt14KD---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image5-4.png)

### 使用量角器和 Selenium WebDriver(本地)并行执行自动化测试

在这个例子中，除了 conf.js 页面中的小改动，我尝试使用与上面相同的脚本。因此，我复制了现有的配置并更新了配置细节，以便在多个浏览器中运行相同的测试来执行跨浏览器并行测试。

```
exports.config = {
  seleniumAddress: 'http://localhost:4444/wd/hub',
  specs: ['lambdatest_spec.js'],
  multiCapabilities: [{
    'browserName': 'chrome',
  },
  {
    'browserName': 'firefox'
  }]
}
```

在上面的例子中，我修改了“多能力”并包含了 firefox 浏览器。这是我们与其他库/包相比的最大优势，在使用量角器和 Selenium 进行自动化测试的情况下，不需要维护。量角器支持内置代码来运行跨浏览器自动化。

[![](img/7d1a7856d547bfcaf70edaa93a527b80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jPsJAie1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image7-4.png)

这一次，我们在 chrome 和 firefox 中运行了相同的测试，并传递了结果。到目前为止，我们已经看到了如何在量角器中运行非角度 JS 应用程序的测试。下一节将展示如何在并行和云执行中运行 AngularJS 应用程序(使用 Lambdatest)。

### 执行 AngularJS 应用的并行执行

为了运行 AngularJS 应用程序的并行执行，配置保持不变，但是我实现了使用 async 和 await 来正确地排队事件。

确保在配置文件中禁用承诺管理器。

```
exports.config = {
  seleniumAddress: 'http://localhost:4444/wd/hub',
  specs: ['calculator_spec.js'],
  multiCapabilities: [{
    'browserName': 'chrome',
  },
  {
    'browserName': 'firefox'
  }],
  SELENIUM_PROMISE_MANAGER: false
}
```

```
describe('To verify super calculator functionality', () => {
    it('perform addition with 2 values', async () => {
        await browser.get('http://juliemr.github.io/protractor-demo/');
        await browser.element(by.model('first')).sendKeys('25');
        await browser.element(by.model('second')).sendKeys('25');
        await browser.element(by.id('gobutton')).click();
        expect(await browser.element(by.css('h2.ng-binding:nth-child(5)')).getText()).toEqual('50')
    })
})
```

执行跨浏览器并行测试，量角器和 LambdaTest(云)

在本地机器上使用多个版本的浏览器运行测试总是有挑战和限制的。你不可能想到在你的本地机器上安装成千上万个可用的浏览器来执行广泛的、自动化的跨浏览器测试。为了实现更多的浏览器使用，在云中远程执行是唯一的解决方案。LambdaTest 提供了 2000 多种浏览器，可以在各种设备和不同的浏览器版本上运行 Selenium 网格上的自动化测试脚本。

在这个例子中，我将解释如何在 LambdaTest cloud provider 上使用量角器和 Selenium 执行跨浏览器并行测试。

**要在 LambdaTest:** 中运行的配置细节

```
exports.config = {

  seleniumAddress: 'https://:@hub.lambdatest.com/wd/hub',

  multiCapabilities: [{
    'name': 'Protractor LambdaTest Parallel_Chrome',
    'build': 'Protractor Javascript Example',
    'browserName': 'Chrome',
    'platform': 'WIN10',
    'version': '70.0',
    'video': true,  //To Enable video recording
    'visual': true, //To Enable step by step screenshot
    'network': true,    //To capture Network logs     
    'console': true   //To capture Console Logs 
  }, {
    'name': 'Protractor LambdaTest Parallel_Safari',
    'build': 'Protractor JavaScript Example',
    'browserName': 'safari',
    'platform': 'macOS High Sierra',
    'version': '11.0',
    'video': true,  //To Enable video recording
    'visual': true, //To Enable step by step screenshot
    'network': true,    //To capture Network logs     
    'console': true   //To capture Console Logs  
  }],
  specs: ['calculator_spec.js'],
  SELENIUM_PROMISE_MANAGER: false
};
```

在上面的截图中:

**硒地址:**'https://:@hub.lambdatest.com/wd/hub'

**用户账户:**您的 LambdaTest 登录账户

**API_KEY:** 这是访问密钥，可以在您登录的个人资料页面中找到。如果没有 API 密钥，请随意生成。

multicapabilities 表示在多个浏览器上并行运行测试。

其他配置主要是特定于平台、浏览器和特定于云提供商的，用于启用或禁用这些选项。

我将在 LambdaTest 上用 Chrome 和 Safari 并行执行以下代码。

```
describe('To verify Angular and non Angular application test', () => {
    it('To verify page title of Lambdattest', () => {
        browser.waitForAngularEnabled(false);
        browser.get("https://lambdatest.com");
        element(by.css('p.home-btn > a.home-cta')).click();
        expect(browser.getTitle()).toEqual('Signup - LambdaTest App | Free Cross Browser Testing Tool');
    });
});
```

[![](img/a34fd2ecc6b44d15b8718d65d2aef6f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j216LgIy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image19-1.png)

以下结果表明测试是成功的，并通过了 lambdatest 云平台。

[![](img/c9c61e0c60e2084df6e253880025b3d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NxuVSsD4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/unnamed-23.png)

让我们看看云中的示例。

[![](img/9f0f96d5d507629e98222fde8071d44a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6ibNfgM4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image23-1.png)

Safari 浏览器的 LambdaTest 页面中测试用例的详细快照:

[![](img/2c87c6e7af3b828afcae2752723a5021.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rtHjyv7a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image29.png)

至此，我们已经看到了如何使用量角器和 Selenium WebDriver 运行简单的 Javascript 自动化测试脚本，用于并行测试的本地和云执行。

### 结论:

通过提供量角器作为一个广泛的端到端测试框架，谷歌已经付出了很多努力，使质量保证工程师的工作变得容易，量角器的工作就像一个魅力。有很多 NPM 模块可以利用 REST API、数据生成器等以及浏览器自动化来集成所有类型的自动化。所有这些都表明量角器是自动跨浏览器测试 AngularJS 和非 AngularJS 应用程序的正确选择。

原文出处:[lambdatest.com](https://www.lambdatest.com/blog/automated-cross-browser-testing-with-protractor-selenium/)