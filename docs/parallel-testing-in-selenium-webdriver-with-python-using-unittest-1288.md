# 使用 unittest 在 Selenium WebDriver 中使用 Python 进行并行测试

> 原文：<https://dev.to/himanshusheth004/parallel-testing-in-selenium-webdriver-with-python-using-unittest-1288>

跨浏览器测试被认为是现代发布周期中不可或缺的任务。然而，手动跨浏览器测试可能会非常费力和耗时。开发人员和测试人员面临的一个主要障碍是跨不同操作系统、浏览器等对他们的网站或 web 应用程序进行浏览器兼容性测试。这就是 Selenium 作为一个免费的开源软件介入的地方！

Selenium 是一个流行的自动化测试框架，用于 web 应用程序或网站的浏览器兼容性测试。Selenium 用于执行自动化功能测试，在实现方面具有较少/最小的学习曲线。

您可以使用 Selenium 进行[自动化跨浏览器测试](https://goo.gl/enWDgT)，它可以在任何浏览器(如 Google Chrome、Mozilla Firefox、Internet Explorer、Edge 等)中自动与您的 web 应用程序/网站进行交互。

在这篇文章中，我们将感人至深。

## Selenium&Python–入门【安装、配置等】

Selenium 为测试人员支持多种顶级编程语言，即 C#、Java、Ruby、Python、Javascript、PHP 等。但是许多开发人员&测试人员选择使用 Selenium 的 Python，因为 Python 比其他编程语言更简洁，库非常广泛，可以帮助你通过[编写更干净、更好的自动化代码](https://goo.gl/azuPQB)来完成更多工作。

既然您已经理解了使用 Selenium 和 Python 进行自动化测试的优势，我们将解释安装和配置的基本步骤。

[ **注意**–在这篇博客中，我们使用 Windows 环境、Python 版和 Eclipse 作为 Selenium IDE]

1.  您需要首先从这里安装 Python。选择适合您的操作系统(如 Windows、Linux、Macintosh)的适当版本。
2.  确保您的路径附加了 Python 可执行文件所在的位置。例如，如果 Python 安装在位置 C:\ Users \ appdata \ local \ programs \ Python \ Python 36；您需要将文件夹位置附加到环境变量 PATH 中。![setpath](img/e1ffd6861774c5e0ec0e8ad1b5a98d63.png)
3.  一旦安装了 Python，下一步就是安装 Selenium 包。它也被称为“Selenium 的 Python 绑定”。这些绑定对于执行 python 自动化测试至关重要。![pipinstall](img/f741a76d56466ac6c70f47804f753892.png)
4.  现在，您需要安装测试框架中最重要的组件 WebDriver。Selenium WebDriver 通过将命令推送到 Web 浏览器来实现浏览器自动化。它是开源 API 的集合，由于它是平台无关的，为 Windows 平台编写的源代码可以在其他操作系统上无缝执行，例如 Linux、Mac 等。想了解更多，可以参考我们的 [Selenium WebDriver 教程跨浏览器测试](https://goo.gl/LttF9i)。本文将重点介绍如何使用 Python 在 Selenium Webdriver 中执行并行测试。

Selenium 需要 geckodriver 来与 Firefox 浏览器通信。为了在 Selenium 中执行并行测试，可以从以下位置下载适用于各种浏览器的 WebDriver:

| **浏览器** | **下载位置** |
| --- | --- |
| 火狐浏览器 | [https://github.com/mozilla/geckodriver/releases](https://github.com/mozilla/geckodriver/releases) |
| 谷歌浏览器 | [https://sites . Google . com/a/chromium . org/chrome driver/downloads](https://sites.google.com/a/chromium.org/chromedriver/downloads) |
| 微软 Edge | [https://developer . Microsoft . com/en-us/Microsoft-edge/tools/web driver/](https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/) |
|  |  |

在下面与 python 自动化测试相关的代码片段中，在导入 Selenium WebDriver 库[第 3-4 行]后，假设 Firefox 安装位置在 PATH 环境变量中更新，并且 geckodriver.exe 在后台执行，开发人员可以通过使用选项 1[第 8 行]调用 Firefox 实例。

选项 2[第 11 行]也可以用于您计划手动调用 geckodriver.exe，从而启动 Firefox 实例的情况。

[![](img/9ec9dfafc746a01ad3b8bb004f3c2c63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DZuZmXSH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/image2-10-1.png)

**Selenium Web Server**–Selenium Web Server 的使用是可选的，这完全取决于您想要使用 Selenium WebDriver 的方式。如果您计划在 Selenium WebDriver 中使用 Python 或任何其他语言通过您的本地机器执行并行测试，并且您有打算用于浏览器兼容性测试的 web 浏览器，那么 WebDriver API 将满足您的需求。Selenium WebDriver 将直接运行浏览器。下面是您需要 Selenium Web 服务器的理想场景:

*   您正在一个浏览器上执行一些测试，这个浏览器只能在远程机器上使用，而不能在本地机器上使用。
*   您正在使用复杂的分布式 [Selenium 网格进行跨浏览器测试](https://goo.gl/s4Q9S1)，其中测试分布在不同的虚拟机(VM)上。
*   您使用的是 HTMLUnit 驱动程序，而不是 Java 绑定，即 C#、Ruby、Python 等。

Selenium Web 服务器可以从[这里](https://www.seleniumhq.org/download/)下载。

## 使用 Python 的 Selenium web driver–用法&示例

既然已经准备好了设置，现在可以使用 Selenium WebDriver Python 绑定来测试您的 web 应用程序。下面是我用于 python 自动化测试的一段简单代码，它将与谷歌的“搜索功能”一起工作。

```
# Import the necessary modules for development 
import time
import unittest
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

# Invoke a new Firefox Instance 
ff_driver = webdriver.Firefox()

# Blocking wait of 30 seconds in order to locate the element 
ff_driver.implicitly_wait(30)
ff_driver.maximize_window()

# Open the Home Page
ff_driver.get("http://www.google.com")

# Look for the Search Element and enter the Search Criteria 
search_criteria = ff_driver.find_element_by_id("lst-ib")
search_criteria.clear()

search_criteria.send_keys("Lambda Test")

# Submit the Search results 
search_criteria.submit()

# Sleep for 10 seconds in order to see the results
time.sleep(10)

# Close the Browser instance
ff_driver.close() 
```

您可以将文件保存为 search.py，并使用以下命令进行编译。
`python search.py`

(**注意**–如果您使用的是 Eclipse 之类的 IDE，您可以使用 Eclipse IDE 中的 PLAY 按钮 PLAY button 编译&执行您的代码)。让我们做一个代码演练，看看上面例子中用于 Python 自动化测试的 Selenium Webdriver 的一些关键部分。
T3![lambdatest](img/5ca15e434455da9426a2aad75efd7d47.png)T5】

导入提供 webDriver 实现的 selenium.webDriver 模块。

```
from selenium import webdriver
from selenium.webdriver.common.keys import Keys 
```

创建一个 Firefox WebDriver 实例。

```
ff_driver = webdriver.Firefox() 
```

打开主页。在任何情况下，WebDriver 都会等到页面完全加载，之后控件返回到脚本。在本例中，我们引入了一个 30 秒的“阻塞等待”呼叫，仅用于演示目的。

```
ff_driver.implicitly_wait(30)
ff_driver.maximize_window()
ff_driver.get("http://www.google.com") 
```

下一步是在网页上定位必要的元素。WebDriver 提供了多种方法来使用 find_element_by*在页面上查找所需的元素。为了找到元素名称，您可以利用浏览器中的“检查元素”功能。您只需右击页面并选择“检查元素”选项，即可找到所需的元素。下面是在谷歌主页上找到关于“输入框”和“谷歌搜索”按钮的详细信息的截图。

[![Inspectelement](img/9f44c425e11c9f774c6d5e9379367db5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GG70K4g9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/Google-Search-1-1.png)

使用 WebDriver 的 clear()方法清除输入框中任何预先填充的文本，并使用从 selenium.webdriver.common.keys 导入的 Keys 类发送按键。一旦输入操作完成，使用 submit()方法调用 submit 以处理搜索。

[![inspectelement](img/d9cac3d2c79c7702afb7f6be6e6e696d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7hxqkguy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/Google-Search-2-1.png)T3】

```
search_criteria = ff_driver.find_element_by_id("lst-ib")
search_criteria.clear()
search_criteria.send_keys("Lambda Test")
search_criteria.submit() 
```

最后，使用 close()方法关闭浏览器会话。

```
ff.driver.close() 
```

现在您已经知道了如何使用 Selenium、Python 和 WebDriver 我们继续下一节，讨论如何使用 Selenium 来编写有效的测试用例。为了使用 [Selenium 编写测试用例，必须导入 unittest](https://goo.gl/PtYBbL) 模块。虽然还有其他选项，如 py.test 和 nose，但我们将重点放在 unittest 上。

注意:如果您希望使用 Pytest 在 Selenium WebDriver 中使用 Python 执行并行测试，您可以参考我们关于使用 Pytest 和 Selenium webdriver 进行自动化测试的博客。

**例二**

```
import unittest
import time
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.chrome.options import Options

class ChromeSearch(unittest.TestCase):

    def setUp(self):
        chrome_options = webdriver.ChromeOptions()
        self.driver = webdriver.Chrome(r"C:/Program Files (x86)/Google/Chrome/Application/chromedriver.exe", options=chrome_options)
        self.driver.maximize_window()

    # As per unittest module, individual test should start with test_
    def test_search_lambdatest_chrome(self):
        driver_chrome = self.driver
        driver_chrome.get('http://www.google.com')
        time.sleep(10) 

        search_criteria = driver_chrome.find_element_by_id("lst-ib")
        search_criteria.clear()
        search_criteria.send_keys("Lambda Test")

        # Check if the search returns any result
        assert "No results found." not in driver_chrome.page_source

        search_criteria.submit()
        time.sleep(10)

    def tearDown(self):
        # Close the browser. 
        self.driver.close()

class FirefoxSearch(unittest.TestCase):
    def setUp(self):
        self.driver = webdriver.Firefox()
        self.driver.maximize_window()

    # As per unittest module, individual test should start with test_
    def test_search_lambdatest_firefox(self):
        driver_firefox = self.driver
        driver_firefox.get('http://www.google.com')
        time.sleep(10) 

        search_criteria = driver_firefox.find_element_by_id("lst-ib")
        search_criteria.clear()
        search_criteria.send_keys("Lambda Test")

        # Check if the search returns any result
        assert "No results found." not in driver_firefox.page_source

        search_criteria.submit()
        time.sleep(10)

    # Anything declared in tearDown will be executed for all test cases
    def tearDown(self):
        # Close the browser. 
        self.driver.close()

if __name__ == "__main__":
    unittest.main() 
```

上面的测试代码可以在 shell 中使用命令‘python file-name . py’来执行。让我们做一个代码演练，并把重点放在代码的重要部分。

在进入实际的实现之前，重要的模块被导入。unittest 是一个基于 JUnit 的内置 Python 模块。它用于组织测试用例，更多关于 unittest 的信息可以在[这里](https://docs.python.org/3/library/unittest.html)找到。与前面的示例类似，导入了支持 webdriver 实现的 selenium.webdriver 模块。

```
import unittest
import time
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.chrome.options import Options 
```

现在我们从实际的测试用例实现开始。ChromeSearch 是测试用例的名称。测试用例类继承自 unittest。测试案例

```
class ChromeSearch(unittest.TestCase): 
```

setup()方法是初始化的一部分，它定义了在测试用例之前执行的指令。在下面的代码片段中，我们创建了一个 Chrome WebDriver 实例，通过 Python 执行 Selenium WebdDriver 进行自动化测试。

```
def setUp(self):
        chrome_options = webdriver.ChromeOptions()
        self.driver = webdriver.Chrome(r"C:/Program Files (x86)/Google/Chrome/Application/chromedriver.exe", options=chrome_options)
        self.driver.maximize_window() 
```

初始化完成后，我们继续测试用例方法。根据 unittest 模块，单个测试应该总是以“test_”开始。在下面的代码片段中，testcase 方法是 test_search_lambdatest_chrome。driver_chrome，它是对在 setUp()方法中创建的驱动程序对象的本地引用。此后，本地引用(在本例中为 driver_chrome)将用于进一步的实现。有关 WebDriver 实现的详细信息，请参考

**示例-1** 我们已经提供了深入的解释。

```
 def test_search_lambdatest_chrome(self):
        driver_chrome = self.driver 
```

tearDown()方法在测试方法完成执行后立即被调用，因为这是执行清理的地方。在下面的代码片段中，关闭操作是在浏览器(即 Chrome)上执行的。

```
def tearDown(self):
        # Close the browser. 
        self.driver.close() 
```

FirefoxSearch testcase 方法[第 34 行]中的实现与 ChromeSearch testcase 方法[第 7 行]相似，只是在该方法中创建了 Firefox WebDriver 实例。最后几行代码是运行测试套件的锅炉板代码。unittest.main()提供了一个命令行界面来执行测试脚本，尽管还有许多其他选项来执行具有更好控制级别的测试。

```
if __name__ == "__main__":
    unittest.main() 
```

我们上面展示的例子把 Firefox 和 Chrome 浏览器放在了整个测试过程的中心。如果你的需求是用安装在 Windows 10 机器上的火狐浏览器(64.0 版)测试你的 web 应用程序呢？在这个过程中，你需要安装相同版本的 Firefox。这种方法不仅昂贵，而且不可扩展。

在测试源代码的过程中，您可能会遇到无法在当前可用的本地基础设施(或设置)上验证 web-app/web 应用程序的情况。执行跨浏览器测试对于确保网页能够在不同的浏览器、不同的操作系统和设备上正确呈现至关重要。这就是 Lambdatest 基于云的跨浏览器基础设施可以用来验证你的网络应用/网站的地方。

## 利用 LambdaTest 平台进行并行测试

在通过源代码使用 Selenium WebDriver 和 Python 进行自动化测试时，您可能会遇到无法在当前可用的本地基础设施(或设置)上验证 web 应用程序的情况，例如，您的要求是使用安装在 Windows 10 机器上的 Firefox 浏览器(版本 64.0)测试您的 web 应用程序。在这个过程中，你需要安装相同版本的 Firefox。这种方法不仅昂贵，而且不可扩展。

通过利用基于云的跨浏览器测试工具，如 LambdaTest，您可以验证您的 web 应用程序在 2000 多种不同浏览器、操作系统、分辨率、设备等上的跨浏览器兼容性。首先，你必须在 [LambdaTest](https://goo.gl/7eP9Lw) 上创建一个账户。一旦你的账户被激活，你需要记下用户名&的访问密钥，它位于你的[个人资料](https://goo.gl/K8Fwrd)中。

[![LambdaTest Profile](img/b92a5de13a4cf2273475115655d0a989.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K2_rTnv6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/123-1.png)

您必须生成必要的功能，即执行测试代码所必需的强制(和可选)要求，例如，功能可以是(平台操作系统–Windows 10，浏览器名称–Firefox，浏览器版本 64.0，Firefox 驱动程序版本–v 0 . 23 . 0)。可以通过访问 LambdaTest 功能生成器生成所需的功能。你必须确保开发语言选择为“Python”。

[![capabilitygenerator](img/f79dae984e6698018536921d22270ac2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ujvQ4jCk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/Lambdatest-Capabilities.png)T3】

```
capabilities = {
        "build" : "your build name",
        "name" : "your test name",
        "platform" : "Windows 10",
        "browserName" : "Firefox",
        "version" : "64.0",
        "selenium_version" : "3.13.0",
        "visual" : true,
        "firefox.driver" : v0.23.0
    } 
```

为了通过 LambdaTest 平台从 Selenium WebDriver 获得自动化测试的最大吞吐量，有必要选择一个提供“并行测试”特性的计划。在他们的[定价页面](https://goo.gl/UtU73v)上可以获得关于计费计划的更多细节。

现在我们的设置已经准备好了，我们执行一个简单的测试，在 Selenium WeDriver 中使用 Python 执行并行测试，在 LambaTest 云平台上进行自动化测试。测试详情如下。

*   **测试 1:** 1。在 Windows 10 2 上调用 Chrome 浏览器(67.0 版)。在 Google 3 上搜索“lambatest.com”。页面呈现后，关闭浏览器并清除测试开始时分配的资源。
*   **测试二:** 1。在 Windows 10 2 上调用火狐浏览器(64.0 版)。在 Google 3 上搜索“lambatest.com”。页面呈现后，关闭浏览器并清除测试开始时分配的资源。

**测试 1 的实施**

```
LambdaTest Selenium automation sample example
Configuration
----------
username: Username can be found at automation dashboard
accessToken:  AccessToken can be generated from automation dashboard or profile section

Result
-------
Execute Python Automation Tests on LambdaTest Distributed Selenium Grid
"""
import unittest
import time
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import urllib3

class LTAutomate(unittest.TestCase):

    """
    Setup remote driver
    Params
    ----------
    platform : Supported platform - (Windows 10, Windows 8.1, Windows 8, Windows 7,  macOS High Sierra, macOS Sierra, OS X El Capitan, OS X Yosemite, OS X Mavericks)
    browserName : Supported platform - (chrome, firefox, Internet Explorer, MicrosoftEdge)
    version :  Supported list of version can be found at https://www.lambdatest.com/capabilities-generator/

    Result
    -------
    """
    def setUp(self):
        # username: Username can be found at automation dashboard
        username="<user-name>" 
        # accessToken:  AccessToken can be generated from automation dashboard or profile section
        accessToken="<access-token>"
        # gridUrl: gridUrl can be found at automation dashboard
        gridUrl = "hub.lambdatest.com/wd/hub"

        desired_cap = {
            'platform' : "win10",
            'browserName' : "chrome",
            'version' :  "67.0",
            "resolution": "1024x768",
            "name": "LambdaTest python google search test ",
            "build": "LambdaTest python google search build",
            "network": True,
            "video": True,
            "visual": True,
            "console": True,
        }

        # URL: https://{username}:{accessToken}@hub.lambdatest.com/wd/hub
        url = "https://"+username+":"+accessToken+"@"+gridUrl

        print("Initiating remote driver on platform: "+desired_cap["platform"]+" browser: "+desired_cap["browserName"]+" version: "+desired_cap["version"])
        self.driver = webdriver.Remote(
            desired_capabilities=desired_cap,
            command_executor= url
        )

    """
    Setup remote driver
    Params
    ----------
    Execute test:  navigate google.com search LambdaTest
    Result
    -------
    print title
    """
    def test_search_in_google(self):
        driver = self.driver
        print("Driver initiated successfully.  Navigate url")
        driver.get("https://www.google.com/ncr")

        print("Searching lambdatest on google.com ")
        time.sleep(8)
        elem = driver.find_element_by_name("q")
        elem.send_keys("lambdatest.com")
        elem.submit()

        print("Printing title of current page :"+driver.title)
        driver.execute_script("lambda-status=passed")
        print("Requesting to mark test : pass")

    """
    Quit selenium driver
    """
    def tearDown(self):
        self.driver.quit()

if __name__ == "__main__":
    unittest.main() 
```

**测试 2 的实施**

```
LambdaTest Selenium automation sample example
Configuration
----------
username: Username can be found at automation dashboard
accessToken:  AccessToken can be generated from automation dashboard or profile section

Result
-------
Execute Python Automation Tests on LambdaTest Distributed Selenium Grid
"""
import unittest
import time
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import urllib3

class LTAutomate(unittest.TestCase):

    """
    Setup remote driver
    Params
    ----------
    platform : Supported platform - (Windows 10, Windows 8.1, Windows 8, Windows 7,  macOS High Sierra, macOS Sierra, OS X El Capitan, OS X Yosemite, OS X Mavericks)
    browserName : Supported platform - (chrome, firefox, Internet Explorer, MicrosoftEdge)
    version :  Supported list of version can be found at https://www.lambdatest.com/capabilities-generator/

    Result
    -------
    """
    def setUp(self):
        # username: Username can be found at automation dashboard
        username="<user-name>" 
        # accessToken:  AccessToken can be generated from automation dashboard or profile section
        accessToken="<access-token>"
        # gridUrl: gridUrl can be found at automation dashboard
        gridUrl = "hub.lambdatest.com/wd/hub"

        desired_cap = {
            'platform' : "win10",
            'browserName' : "firefox",
            'version' :  "64.0",
            "resolution": "1024x768",
            "name": "Parallel Test - LambdaTest python google search test on Firefox",
            "build": "Parallel Test - LambdaTest python google search build on Firefox",
            "network": True,
            "video": True,
            "visual": True,
            "console": True,
        }

        # URL: https://{username}:{accessToken}@hub.lambdatest.com/wd/hub
        url = "https://"+username+":"+accessToken+"@"+gridUrl

        print("Initiating remote driver on platform: "+desired_cap["platform"]+" browser: "+desired_cap["browserName"]+" version: "+desired_cap["version"])
        self.driver = webdriver.Remote(
            desired_capabilities=desired_cap,
            command_executor= url
        )

    """
    Setup remote driver
    Params
    ----------
    Execute test:  navigate google.com search LambdaTest
    Result
    -------
    print title
    """
    def test_search_in_google(self):
        driver = self.driver
        print("Driver initiated successfully.  Navigate url")
        driver.get("https://www.google.com/ncr")

        print("Searching lambdatest on google.com ")
        time.sleep(8)
        elem = driver.find_element_by_name("q")
        elem.send_keys("lambdatest.com")
        elem.submit()

        print("Printing title of current page :"+driver.title)
        driver.execute_script("lambda-status=passed")
        print("Requesting to mark test : pass")

    """
    Quit selenium driver
    """
    def tearDown(self):
        self.driver.quit()

if __name__ == "__main__":
    unittest.main() 
```

让我们对测试 1 进行一次代码演练(注意——除了被调用的浏览器类型之外，测试 2 的其余功能都是相同的)。

所有必需的模块都在开始时导入(unittest、selenium 等。)

您需要设置测试执行所需的必要能力。我们建议您利用 [LambdaTest 能力生成器](https://goo.gl/vTHnmt)(之前已经讨论过)来生成“期望的能力”。

```
desired_cap = {
            'platform' : "win10",
            'browserName' : "firefox",
            'version' :  "64.0",
            "resolution": "1024x768",
            "name": "Parallel Test - LambdaTest python google search test on Firefox",
            "build": "Parallel Test - LambdaTest python google search build on Firefox",
            "network": True,
            "video": True,
            "visual": True,
            "console": True,
        } 
```

在 setUp()方法中，您应该输入用于访问 LambdaTest 平台的用户名& access-token，以便使用 Selenium Webdriver 和 Python 进行自动化测试。如果这些凭据不正确，您可能无法执行代码。因为执行是在云上执行的，所以我们使用“远程 WebDriver API”而不是“本地 WebDriver API”。“远程 WebDriver API”的输入是“所需功能”和“命令执行 URL”(使用[用户名+访问令牌+网格 URL]的串联创建)。

现在，您可以在 Selenium WebDriver 中使用 Python 执行并行测试，以进行自动化测试。请注意使用“python”命令在不同终端上进行的两个测试。

[![](img/d747a15713ead5b62864ea612173cd49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HaKMe8kC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/Parallel-tests-in-action-1.png)

以便检查测试、日志、元数据等的状态。；您需要检查 Lambatest 上的“自动化”选项卡，即[https://automation.lambdatest.com](https://automation.lambdatest.com)

[![](img/ba03005b097ab9d2dd91c3a6c82c5827.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KjyDtKr0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/Lambdatest-Parallel-Testing-Console-1.png)

如下面的自动化快照所示，有一个提到有多少测试正在执行/运行的字段。在我们的例子中，我们在 Selenium WebDriver 中使用 Python 执行了并行测试，使用两个并行测试(即并发会话)进行自动化测试。因此，当您调用测试(parallel _ test _ example-1 . py & parallel _ test _ example-2 . py)时，它们是并行执行的。通过这种方式，您可以以一种显著的方式加速整个测试用例的执行。

[![](img/9e4c02b40dcf59febbae4b22ea7b8ad0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9q6SdpIz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/Parallel-Testing-Is-Enabled.png)

## 结论

使用 Python 在 Selenium WebDriver 中执行并行测试来进行自动化测试，可以通过加速缓慢的自动化 UI 测试来节省大量时间。如果测试数量较少，您可能不需要在 Selenium WebDriver for Python 或任何其他语言中进行并行测试，但是一旦项目的复杂性增加(或者要执行的测试数量增加)，并行测试就是在尽可能短的时间内获得最佳结果的理想机制。如今，更多的团队关注于在整个软件开发&测试过程中利用[持续集成(CI)和持续交付(CD)](https://goo.gl/J7P7aU) 。充分利用‘并行测试’对于持续集成(CI)的实现非常重要。

使用 LambdaTest 在云上进行自动跨浏览器测试，测试时间可以大大减少，因为您还可以灵活地并行执行多个测试。它有强大的功能(如时间线，自动化日志，分析等。)这将在分析和调试测试时派上用场。测试愉快！

[![lambdatest](img/5ca15e434455da9426a2aad75efd7d47.png)](https://goo.gl/FR8AR1)

原文出处:[lambdatest.com](https://goo.gl/kHgAWi)

**相关文章**

1.  [敏捷团队如何使用测试自动化金字塔？](https://goo.gl/HPpe5A)
2.  [Selenium Grid 跨浏览器测试设置教程](https://goo.gl/4UuUqS)
3.  [使用 Pytest 和 Selenium WebDriver 进行自动化测试](https://goo.gl/WRzXoi)