# 使用 Watir 的跨浏览器自动化测试

> 原文：<https://dev.to/lambdatest/cross-browser-automation-testing-using-watir-5g83>

我们生活在一个软件开发需要自动化的时代。诸如 RAD(快速应用程序开发)、Agile 等软件开发方法要求您将[自动化测试](https://www.lambdatest.com/blog/automated-cross-browser-testing/)作为您发布周期的一部分。存在许多用于自动化测试的测试自动化框架。今天，我将学习 Watir，一个用于浏览器自动化的基于 selenium 的开源 web 驱动程序。使用 Watir 进行跨浏览器自动化测试将有助于您确保 web 应用程序的用户界面呈现良好。如果你是自动化测试的初学者，不知道基础知识，那么不要担心，因为我也会谈到浏览器自动化，[跨浏览器自动化]，并行测试，以及 Watir 与其他工具和库的不同之处。事不宜迟，我们开始吧！

### 什么是浏览器自动化？

浏览器已经成为我们日常工作中不可或缺的一部分。我们用浏览器探索、学习、购物、投资和做更多的事情。有时候我们觉得需要浏览器来自动化我们重复的任务。想象一下那会有多酷？可能吗？是的，你在浏览器中执行的任何任务都是自动化的。

让我们根据基本的实际情况进行一些考虑。在以下情况下，您可以使用浏览器自动化:

*   你需要填写很多表格来输入数据。
*   您需要每天从门户网站生成一份报告，并向团队发送电子邮件。
*   你导航到一个特定的网站，每天投票支持一个电视真人秀节目中的特定候选人。

在软件行业，测试一个 web 应用程序需要投入大量的时间、资源和金钱。如果仅仅依靠手工测试，测试一个 web 应用程序来检查它的表现会非常费力。手动测试确保您的应用程序根据客户的需求和期望正常运行。浏览器自动化拯救了我们，因此我们可以避免重复的手动测试、回归测试用例，并通过减少工作量获得早期反馈。

### 什么是跨浏览器自动化测试？

现在，互联网上有成千上万的浏览器，你的网站可以在每一个浏览器或浏览器版本上以独特的方式呈现。对你的网站进行全面测试可能会非常复杂。[跨浏览器自动化测试](https://www.lambdatest.com/blog/automated-cross-browser-testing/)是验证您的应用程序在不同浏览器及其组合版本之间无缝呈现。这主要是为了在运行不同浏览器的各种操作系统组合上检查您的可发货应用程序功能的[跨浏览器兼容性](https://www.lambdatest.com/blog/how-to-make-a-cross-browser-compatible-website/)，如[谷歌 Chrome](https://www.lambdatest.com/test-on-chrome-browsers) 、 [Mozilla Firefox](https://www.lambdatest.com/test-on-firefox-browsers) 、 [Edge](https://www.lambdatest.com/test-on-edge-browsers) 、 [IE](https://www.lambdatest.com/test-on-internet-explorer-browsers) 、 [Opera](https://www.lambdatest.com/test-on-opera-browsers) 、 [Safari](https://www.lambdatest.com/test-on-safari-browsers) 、 [Yandex](https://www.lambdatest.com/test-on-yandex-browsers) 等等。

要运行所有的浏览器及其组合版本是很费时间的。例如，您的客户要求对 Chrome、Safari、Firefox 和 Edge 浏览器的 5 个最新版本和 5 个版本进行定期的[跨浏览器兼容性](https://www.lambdatest.com/feature)检查，这将使浏览器组合总数达到 40 种完全不同的独特浏览器及其一个测试案例的版本。想想看，对所有这些浏览器组合执行跨浏览器兼容性检查并提供签准是多么的耗时和乏味，这需要积极合理的规划、对 QA 资源的巨大投资、应对挑战、时间和成本。为了处理这个问题，针对并行测试会话的跨浏览器自动化测试是解决方案。

### 什么是跨浏览器并行测试自动化？

[跨浏览器并行测试](https://www.lambdatest.com/selenium-automation)用于同时跨多个浏览器组合运行单一测试。这是自动化测试的一个非常实用和强大的消费场景。跨浏览器并行测试自动化允许您缩减执行时间，同时不影响您的检查覆盖率，并导致您的测试结果的更快循环。

### 什么是水？

Watir 是一个开源的 Ruby 库，有助于实现跨浏览器的自动化测试。Watir 支持 Ruby，这是一种面向对象的语言，通常比其他语言更简单、更快。Watir 的好处是，它支持任何 web 应用程序，不管开发该应用程序使用的是什么技术。

### 为什么是 Watir？

*   这是一个免费的开源工具，属于 Ruby 家族。
*   它支持无头浏览器执行。
*   它支持页面对象设计模式。
*   它支持黄瓜集成。
*   测试可以保持简单和灵活。
*   无论使用何种技术开发您的应用程序，它都支持您的 web 应用程序。
*   它支持不同平台上的多种浏览器。
*   它重量轻，易于使用。
*   它支持通过基于云的跨浏览器自动化测试工具(如 LambdaTest)在云中执行。

在本文中，我们将看看如何使用 RubyMine IDE 为 Watir 设置测试自动化环境，然后继续使用示例脚本。我还会展示如何运行脚本，并在 RubyMine 中看到失败。让我们开始吧。

### 使用 Watir 开始跨浏览器自动化测试

以下是在 Windows 上使用 Watir 执行跨浏览器自动化测试所需的先决条件:

*   RubyInstaller
*   瓦特尔
*   RubyMine IDE

RubyInstaller 是必须的，因为 Watir 支持 Ruby 代码，所以在我们进行自动化之前，这个安装非常重要。

### 设置自动化环境

本节详细介绍了如何设置自动化环境，以便使用 Watir 通过 Windows 执行跨浏览器自动化测试。

**第一步:安装 Ruby:**

导航到官方的 Ruby 安装页面[这里](https://rubyinstaller.org/downloads/)。

点击最新版本的 DEVKIT 下载。选择您的操作系统架构类型，例如，我选择(X64) 64 位操作系统。
[![IMAGE](img/0cd9f425a884f5a268853a99756cebd6.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--k2M_wLZ2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/unnamed-12.png)

下载后，右键单击安装程序，并在 Windows 机器上以管理员身份运行。
[![IMAGE](img/2a108a8b66d6f74830a6aa90613200bd.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--3AxGjvOx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image1-7.png)

设置窗口弹出接受许可协议，你需要接受这一点，并继续下一步。

[![IMAGE](img/20ed1a59ad42f96429f2a98933704440.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0zGgne4I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image10-2.png)

我建议选择所有复选框，然后点击安装按钮继续。

[![IMAGE](img/5829d3f2b78a8c319587de25def0d886.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Evb3YI_---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image7-2-1.png)

下面的进度条显示安装正在进行中，安装 ruby 需要几分钟时间。

[![IMAGE](img/9ec10af12dcffc35c9fa3ba0484893ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Kf7xJehG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/unnamed-13.png)

安装完成后，将弹出以下窗口来安装更新的组件。这是可选的，你可以通过关闭窗口或点击“回车”按钮来更新所有组件。

[![IMAGE](img/705a0825e0b3427e51a752ab8f6b7f02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UsO_4ene--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image16.png)

在 windows 中打开命令提示符，输入以下命令:

*   ruby-version 命令提示符中显示 ruby version 的安装版本，表示 Ruby 安装成功。![image](img/478b3ca39353b88337dafea06e76c79d.png)

再次验证安装成功，Ruby 程序工作正常。输入以下命令:

*   抗高血压药

irb 或 IRB 代表交互式 Ruby Shell，它是面向对象脚本语言 Ruby 中用于编程的 REPL(读取-评估打印循环)。

我将编写一个简单的代码，将“[lambdatest.com](http://lambdatest.com/)”打印出来。

[![image](img/94cb86a3ebc3cb066d87360b5d6a1709.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ez9_tsYn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/unnamed-14-1.png)

**第二步:安装水:**

Watir 的安装非常简单。正如我在上面提到的验证 Watir，你必须首先安装 Ruby。要安装，请提供以下 gem 命令:

*   gem 安装 watir

gem 表示 RubyGems，它是 Ruby 编程的一个包管理器，提供了一个扰乱 Ruby 库的标准格式。

[![image](img/5800a29b79c63e80f67eaedca7f61369.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cTWuHnYz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image23.png)

**第三步:安装 RubyMine IDE:**

RubyMine 是一个 IDE(集成开发环境)，可以帮助你编写、调试和测试应用程序的代码。Ruby 还支持多种操作系统，如 Windows、MacOS 和 Linux 等。

注意:RubyMine 提供 30 天免费试用许可。

要下载 RubyMine，点击[这里](https://www.jetbrains.com/ruby/)。

单击下面的按钮，它将下载到您的机器中。
[![](img/88d796a65fa10ac9874d690f2bd2f08e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--DdMo9DE5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image3-3.png)

右键单击安装程序并以管理员身份运行，弹出以下窗口。

点击下一步，继续直到安装完成，并启动 RubyMine

[![image](img/791ed75d4ea9d2fa7de06f47af9e9e41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Apn78-aW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image20.png)

保持现有插件的默认设置，然后点击下一步，直到你看到下面的窗口。

单击“创建新项目”

[![image](img/9e472f6cdee6562ff5b5a2db5f82841a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wsp3NTH0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image13.png)

输入项目名称并选择“Ruby SDK ”,如果您安装了 Ruby Installer，它将可用。

[![image](img/63a0743f1db34cf70e8c810c11c71d33.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JJixZ8UX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image18.png)

右键单击创建的项目，并创建一个名为“testsuites”的新目录

[![image](img/6efdbabb9ba54a5982b77940f6ed86be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T_s9D7c2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image9-3.png)

在 testsuites 下创建一个名为“resources”的子目录，并删除“chromedriver.exe”

要运行基于浏览器的自动化，需要 chromedriver，这是一个单独的可执行文件，WebDriver 使用它来控制 chrome。类似地，我们对每个浏览器都有不同的驱动程序，比如 Mozila firefox 的 geckodriver，微软 edge 浏览器的 Edge 驱动程序等等。,

要下载 chromedriver，请访问[这里](http://chromedriver.chromium.org/downloads)。

### 编写样本测试代码

用 RubyMine 写代码非常简单。

右击 testsuites 并选择“TestUnit 测试模板”
[![image](img/36f7cdd26af459dba9d638f947ea5477.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x9uAZQp2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image19.png)

在文件名中输入文件名“my_script.rb ”,然后单击确定按钮。

[![](img/c1d1235cbaa9666973beedbf115c7070.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KTJF_7aT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image5-2.png)

在您现在正在观察的 RubyMine 窗口上，编写以下代码。

**代码(自我说明):**

```
require 'watir'
require 'selenium-webdriver'
require 'test/unit'
require 'test/unit/ui/console/testrunner'

class Sampletest < Test::Unit::TestCase

  "" "
    LambdaTest Watir automation sample example
    Configuration
    ----------
    Download Chrome driver from http://chromedriver.chromium.org/downloads and drop into your resource folder

    Result
    -------
    Execute Watir Automation Tests on LambdaTest website
    " ""

  def setup
    "" "
        Setup local driver
        Params
        ----------
        platform : Windows 10
        browserName : Supported platform - (chrome in your local box)

        Result
        -------
        " ""
    #Initializing chrome driver
    Selenium::WebDriver::Chrome.driver_path = "resources/chromedriver.exe"
    @browser = Watir::Browser.new
  end

  def test_verifyLambdapage()
      #Navigate to lambdatest.com website
      @browser.goto 'https://lambdatest.com'
      #Maximize the browser window
      @browser.window.maximize
      #Initializing element with Link text
      ele_starttestingbutton = @browser.link(:text => "START TESTING")
      #Perform click operation
      ele_starttestingbutton.click
      #printing page title
      puts("Actual Page title is: "+@browser.title)
      #Verifying actual and expected title of the page. This examples I intentionally fail the test by adding spell error
      assert_equal("Signup - LambdaTest App | Free Cross Browser Testing To0ol", @browser.title)
  end

  def teardown
    #Quit the browser
    @browser.quit
  end
end
```

右键单击脚本并在 IDE 中选择“Run ”,查看测试结果和输出

[![](img/f865d9c40c45f08afce8c5f294c489ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wc4_d7Jm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image11-2.png)

上面的测试脚本故意不验证实际页面标题与预期页面标题的比较结果。

下面的快照解释了测试失败的原因，并且用颜色突出显示了差异。

[![](img/68aba5334a265a1de72b321e7d33c514.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JujF_-Gr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image22.png)

### 使用本地 WebDriver 通过 Watir 执行跨浏览器自动化测试

**代码解释:**

```
def setup
  "" "
      Setup local driver
      Params
      ----------
      platform : Windows 10
      browserName : Supported platform - (chrome in your local box)

      Result
      -------
      " ""
  #Initializing chrome driver
  Selenium::WebDriver::Chrome.driver_path = "resources/chromedriver.exe"
  @browser = Watir::Browser.new
end
```

```
def teardown
  #Quit the browser
  @browser.quit
end
```

在 TestUnit 模板中，IDE 自动创建 def setup 和 teardown。这些方法实际上在测试前后运行。

**设置**:这个方法是运行测试的先决条件，你可以设置类似初始化浏览器或任何测试数据设置

**Teardown** :该方法在您的测试运行之后，您可以关闭、退出浏览器或删除数据库中的数据等。,

```
Selenium::WebDriver::Chrome.driver_path = "resources/chromedriver.exe"
    @browser = Watir::Browser.new
```

上面的代码正在初始化 chrome 驱动程序，创建新的实例并将对象引用分配给变量 browser。

下一行代码是典型的 Watir 脚本，实际步骤如下:

注意:以“test_”开头的方法名是强制性的，哪个单元测试运行者实际上意识到这是测试方法。

*   导航到浏览器
*   最大化窗口
*   点击[lambdatest.com](http://lambdatest.com/)中的“开始测试”按钮
*   打印当前页面标题
*   最后验证预期标题与实际标题是否相符

```
def test_verifyLambdapage()
    #Navigate to lambdatest.com website
    @browser.goto 'https://lambdatest.com'
    #Maximize the browser window
    @browser.window.maximize
    #Initializing element with Link text
    ele_starttestingbutton = @browser.link(:text => "START TESTING")
    #Perform click operation
    ele_starttestingbutton.click
    #printing page title
    puts("Actual Page title is: "+@browser.title)
    #Verifying actual and expected title of the page. This examples I intentionally fail the test by adding spell error
    assert_equal("Signup - LambdaTest App | Free Cross Browser Testing To0ol", @browser.title)
end
```

### 在 LambdaTest 使用远程 WebDriver，使用 Watir 执行跨浏览器自动化测试

使用 Watir for Local WebDriver 执行[跨浏览器自动化测试](https://www.lambdatest.com/selenium-automation)的缺点是，您只能在本地机器上安装的浏览器上测试您的脚本。然而，在你的系统上安装成千上万的浏览器是不可行的。这就是为什么云是执行跨浏览器自动化测试的更好的平台。云提供商提议在 Selenium grid 的支持下运行您的测试脚本。最重要的一点是，您不必被迫维护或进行任何必要的设置，您所希望做的只是稍微调整一下代码来支持 selenium grid。我将解释如何调整相同的代码以在 Lambdatest 中运行测试。LambdaTest 提供了一个广泛的 Selenium 网格，提供 2000 多种浏览器供测试。您甚至可以使用 SSH 隧道对本地托管的 web 应用程序执行跨浏览器自动化测试。让我展示如何使用 Watir 将 LambdaTest 用于浏览器自动化。

### 使用 Watir 在 LambdaTest 中运行单个实例

LambdaTest 提供跨浏览器并行测试，并行运行您的跨浏览器自动化测试脚本，以减少巨大的执行工作量。我们将在下面详细介绍如何运行简单测试，以及如何在 LambdaTest 中用不同的浏览器运行相同的测试集。

我使用的是我在上面的前一节中连接的相同脚本，除了在下面的快照中提到的设置技术的后续变化。

```
def setup
  "" "
      Setup remote driver
      Params
      ----------
      platform : Supported platform - (Windows 10, Windows 8.1, Windows 8, Windows 7,  macOS High Sierra, macOS Sierra, OS X El Capitan, OS X Yosemite, OS X Mavericks)
      browserName : Supported platform - (chrome, firefox, Internet Explorer, MicrosoftEdge)
      version :  Supported list of version can be found at https://www.lambdatest.com/capabilities-generator/

      Result
      -------
      " ""
  username = "USER_NAME"
  accessToken = "PASSWORD"
  gridUrl = "beta-hub.lambdatest.com/wd/hub"
  caps = Selenium::WebDriver::Remote::Capabilities.new
  caps[:browserName] = "chrome"
  caps[:version] = "67.0"
  caps[:platform] = "win10"
  caps[:name] = "LambdaTest Watir Signup Page Name verification"
  caps[:build] = "LambdaTest Watir Signup Page Name verification"
  caps[:network] = true
  caps[:visual] = true
  caps[:video] = true
  caps[:console] = true
  puts(caps)
  # URL: https://{username}:{accessToken}@beta-hub.lambdatest.com/wd/hub
  @browser = Watir::Browser.new(
      :remote,
      :url => "https://" + username + ":" + accessToken + "@" + gridUrl,
      :desired_capabilities => caps)
end
```

LambdaTest 支持 Selenium grid，因此要在 LambdaTest 中运行您的测试，您需要初始化远程 webdriver 并指向您的 Selenium hub 或服务器正在运行的位置。

要在 LambdaTest 中运行的 Selenium 网格 URL 的模板是:

[https://{用户名}:{ access token } @ beta-hub . lam datest . com/wd/hub](https://%7Busername%7D:%7BaccessToken%7D@beta-hub.lamdatest.com/wd/hub)

用户名:可以在 LambdaTest 仪表板页面找到。

访问令牌:您可以在您的个人资料页面上找到它。如果没有令牌，请随意单击“生成访问令牌”按钮。

网格 URL:[beta-hub.lamdatest.com](http://beta-hub.lamdatest.com/)selenium 网格运行的地方。

另一个重要的特性是[功能生成器](https://www.lambdatest.com/capabilities-generator/):要在 LambdaTest Selenium grid 中运行您的测试套件，您必须设置所需的功能，例如，您想要在哪个浏览器、哪个操作系统等基础配置上进行测试。

您可以配置哪个浏览器、平台等。，最后它会根据您的选择生成一个代码。下面是 Ruby 语言的结果。
[![capability-generator](img/7dd0915e58a63fc3d3733abf0bd810cb.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--DpZ_Sv_M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image12-2.png)

Capabilities Generator 还支持 Java、JavaScript、C#、Python 和 PHP。

这就是使用 Watir 在 LambdaTest 成功运行跨浏览器自动化测试所需的全部内容。

### 使用 LambdaTest 运行 Watir 跨浏览器并行测试

到目前为止，您已经看到了在 LambdaTest 中运行单个实例方法，本节将详细介绍如何在 LambdaTest 中使用完全不同的浏览器运行相同的测试集。本节将帮助您掌握一种在 Watir 中运行不同浏览器通用代码的方法。

以下代码将有助于实现并行执行:

```
require 'watir'
require 'selenium-webdriver'
require 'test/unit'

class DumTest < Test::Unit::TestCase

  "" "
    LambdaTest Watir automation sample example with Cross Browsers
    Configuration
    ----------
    username: Username can be found at automation dashboard or profile page
    accessToken:  AccessToken can be generated from automation dashboard or profile section

    Result
    -------
    Execute Watir Automation Tests on LambdaTest using Selenium Grid
    " ""

 #Confuring Multiple browser and its versions
  BROWSERS = {firefox: '64',
              chrome: '67'}

  def setup (browser_name, browser_version)
    username = "USER_NAME"
    accessToken = "PASSWORD"
    gridUrl = "beta-hub.lambdatest.com/wd/hub"

    #Initializing Capabilities
    caps = Selenium::WebDriver::Remote::Capabilities.new
    caps[:browserName] = browser_name
    caps[:version] = browser_version
    caps[:platform] = "win10"
    caps[:name] = "LambdaTest Watir Signup Page Name verification"
    caps[:build] = "LambdaTest Watir Signup Page Name verification"
    caps[:network] = true
    caps[:visual] = true
    caps[:video] = true
    caps[:console] = true

    puts(caps)
    # URL: https://{username}:{accessToken}@beta-hub.lambdatest.com/wd/hub
    Thread.current[:@driver] = Watir::Browser.new(
        :remote,
        :url => "https://" + username + ":" + accessToken + "@" + gridUrl,
        :desired_capabilities => caps)
  end

  #Using Thread to achieve parallel execution
  def runthreads
    threads = []
    BROWSERS.each_pair do |browser, browser_version|
      threads << Thread.new do
        setup(browser, browser_version)
        yield
        teardown
      end
    end
    threads.each {|thread| thread.join}
  end

  # Called after every test method runs. Can be used to tear
  # down fixture information.

  def teardown
    Thread.current[:@driver].quit
  end
end

#Test Runner
#Instantiate test method and use threads to run paralle execution
mytest = DumTest.new(self)
mytest.runthreads do
  Thread.current[:@driver].goto "https://lambdatest.com"
  Thread.current[:@driver].window.maximize
  Thread.current[:@driver].link(:text => "START TESTING").click
  if "Signup - LambdaTest App | Free Cross Browser Testing Tool" == Thread.current[:@driver].title
    puts("Test is passed")
  else
    puts("Test is failed")
  end
end
```

[![](img/1cf0bdda1385641997dd63fb0a620cb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ro8gGgTV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image17.png)
[![](img/67ee18a4b35eedc77c70d3ebe77c475d.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--2O9jX5V5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image21.png)

逻辑很简单，使用线程概念来并行运行同一组测试脚本。该线程根据 browsers 变量中添加的浏览器数量及其版本创建动态。

出于演示目的，我在设置方法中传递了参数 username、password，但为了进行更多自定义，请包含多个参数并替换为功能。

好了，现在你已经在 LambdaTest 云平台上查看了你的测试结果。请看看下面的快照。

[![](img/027625b5ffeb80086aa449bea816437a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KxUx5NYD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/image15.png)

太棒了。我们成功了，有了这个，你已经成功地在 LamdaTest 云平台上使用 Watir 运行了你的第一个跨浏览器自动化测试脚本。

LambdaTest 为每个测试提供了非常深入的细节。如果您查看下面的屏幕截图，您将能够注意到环境详细信息、状态、开始时间、结束时间以及自动显示的更多信息。您还可以找到测试会话的视频日志，以及时间轴旁边的分析相关选项卡。

[![](img/dee5117ef17cf88ae42037ef533ba5b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fB7K9ChP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/01/Image026_test_results_.png)

这就是你开始使用 Watir at LambdaTest 进行跨浏览器自动化测试所需要知道的全部内容。我很想在评论区知道你对这个教程的想法和反馈。如果你有任何问题要问我，我会很感激。测试愉快！

原文出处:[lambdatest.com](https://www.lambdatest.com/blog/cross-browser-automation-testing-using-watir/)