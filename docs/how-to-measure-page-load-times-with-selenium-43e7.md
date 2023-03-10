# 如何用 Selenium 测量页面加载时间？

> 原文：<https://dev.to/lambdatest/how-to-measure-page-load-times-with-selenium-43e7>

在任何软件产品的开发和发布过程中，都要考虑许多度量标准。其中一个衡量标准是“用户体验”,它的核心是你的客户使用你的产品的难易程度。你可能已经开发了一种产品，可以解决大规模的问题，但是如果你的客户在使用它时遇到困难，他们可能会开始寻找其他的选择。提供更好的网页设计、页面加载速度、可用性(易用性)、内存需求等的网站或网络应用程序。今天，我将向您展示如何使用 Selenium 为自动化跨浏览器测试测量页面加载时间。在此之前，我们应该了解一个网站或 web 应用程序的页面加载时间的相关性。

## 为什么要重点测量页面加载时间？

如果加载时间超过 3 秒，超过 40%的网站访问者可能会离开。这就是为什么您的软件开发、设计和产品开发团队应该关注于提高您的 web 产品的性能。不相信我？下面是谷歌进行的一项[研究的图片，该研究涉及页面加载速度与跳出率](https://think.storage.googleapis.com/docs/mobile-page-speed-new-industry-benchmarks.pdf) [![Page Load Time](img/83cd16dea9bb8d7f9cd38cfbc1fd857b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1UcPildR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/03/pasted-image-0-39-1.png)

流行的搜索引擎测量接收第一个字节所花费的时间，以确定站点速度/页面速度。因此，渲染速度慢的页面会对搜索引擎排名产生巨大影响。页面速度慢也意味着搜索引擎爬虫可以在分配的爬行预算内爬行较少数量的页面，这将最终显示在排名中。

我们大多数人都知道的一个事实是，如果一个网站在移动设备上提供更友好的用户体验和更快的页面加载时间，谷歌会更重视这些网站，并在移动搜索结果中排名第一。为此，谷歌在 2015 年 4 月 21 日提出了一种新的移动友好算法。因此，[测试一个移动网站](https://www.lambdatest.com/blog/how-to-test-a-mobile-website-using-lambdatest/?utm_source=Dev&utm_medium=Blog&utm_campaign=hi-05-200219us&utm_term=OrganicPosting)的用户友好性是很重要的。

不仅仅是谷歌，如果网站上的页面不能以闪电般的速度加载，你的客户也不会喜欢你的网站或网络应用。即使页面加载时间延迟一秒钟，也会导致更高的跳出率和页面浏览量的减少。这对点击率(CTR)和转化率也有负面影响。简而言之，衡量页面加载时间(也称为页面速度)就是内容/页面出现在屏幕上所需的时间。页面加载时间越短，产品的可用性就越好，你的转化率就越高。

## 衡量页面加载时间要考虑的指标

同一网站/web 应用程序上的不同页面可能有不同的加载时间，这取决于页面开发所使用的设计。开发人员可能使用了这两种技术中的任何一种来减少页面加载时间。

a.用于图像优化的文件压缩技术。
b .浏览器缓存。
c .减少重定向次数。
d .减少非阻塞 JavaScript 的使用。
e .尺寸可变的图像，取决于客户使用的设备

这是从开发者的角度来说的，但是你如何通过测量每个页面的页面加载时间来测试网站呢？

你的网站上可能有数百个网页。别忘了，你必须执行浏览器兼容性测试，以了解你的网站上的每一页在数百种浏览器和浏览器版本中的页面加载时间。

如果从不同的浏览器(包括浏览器版本)访问，您的网页加载时间可能会有所不同。它还取决于平台(台式机、平板电脑、手机等)。)以及访问您的网站/网络应用的地理位置。你不能手动测试每个组合(**网页+浏览器+浏览器版本+操作系统**)。这就是 [Selenium grid](https://www.lambdatest.com/blog/selenium-grid-setup-tutorial-for-cross-browser-testing/?utm_source=Dev&utm_medium=Blog&utm_campaign=hi-05-200219us&utm_term=OrganicPosting) 帮助您对网页进行自动跨浏览器测试的地方。您可以使用 Selenium 执行自动化测试来测量页面加载时间。在此之前，看一下**导航计时**很重要。

嘿，你在寻找一个免费的工具来根据 RFC 4122 创建第 4 版通用唯一标识符吗？版本 4 UUIDs 是随机动态生成的。你可以通过[随机 UUID 生成器](https://www.lambdatest.com/free-online-tools/random-uuid-generator?utm_source=devto&utm_medium=organic&utm_campaign=jul27_rn&utm_term=rn&utm_content=free_tools)工具来实现。

## 利用导航计时来衡量页面加载速度

那么，如何衡量页面加载速度呢？**导航计时**是一个流行的 JavaScript API，可以用来精确测量网络上的性能。虽然有其他可用的库，导航时间更可靠&准确。

导航定时 API 可用于大多数流行的网络浏览器，即 Firefox、Chrome、Internet Explorer 等。尽管有许多接口可用于导航计时，但大多数与页面加载时间相关的细节都可以使用 window.performance 对象的属性来访问。window.performance 返回更准确的结果，因为 API 利用 HRT(高分辨率时间)来计算与网页相关的必要时间。

window.performance 对象的重要属性有

**导航**–关于用户如何导航到网页的详细信息。

**【定时】**–关于导航的信息&页面加载事件。

作为导航定时 API 的一部分的其他可用接口有**performance Navigation Timing**、 **PerformanceTiming** 、 **PerformanceNavigation** 。借助 **XHR** (XMLHttpRequest)，性能数据从客户端推送到服务器端。使用导航计时 API，您可以记录每个请求。加载新页面花费的时间，卸载前一页面花费的时间，查找域名遇到的时间，窗口。加载总时间、响应开始时间等。是可以通过 window.performance 属性访问的一些属性。下图显示了 **PerformanceTiming** 界面的元素。

[![PerformanceTiming](img/5b801042e2127e3b4afb49ac65f8561e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---yGcr6VW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/03/pasted-image-0-40-1.png)

timing 属性返回关于当前页面/文档的重要性能信息，例如关于 navigationStart、connectStart、connectEnd、domainLookupStart、domainLookupEnd 等的详细信息。

下图显示了导航定时事件的执行顺序。每个 performance.timing 属性显示导航事件的时间，即前一页何时被卸载？请求最新页面的时间。文档对象模型(DOM)何时开始加载页面？还有更多。时间以毫秒为单位，从 1970 年 1 月 1 日午夜(UTC)开始计算。你可以在 PerformanceTiming interface 上看一下[W3C 官方文档，以便获得关于每个事件的更多信息。](https://www.w3.org/TR/navigation-timing/#sec-navigation-timing-interface)

[![order of performance of navigation.timing](img/b1f6eade84515c4f433593de1dcbfb36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Iasp0Ut3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/03/image3-4.png)

虽然您可以通过源代码访问 window.performance，但是为了查看 window.performance 的运行情况，您可以通过浏览器(Firefox、Chrome 等)的调试控制台访问该选项。).比如在火狐/Chrome 浏览器中打开 [LambdaTest](https://www.lambdatest.com/?utm_source=Dev&utm_medium=Blog&utm_campaign=hi-05-200219us&utm_term=OrganicPosting) 网站。在我们的例子中，我们使用的是 Firefox 浏览器。现在打开**检查元件**选项，进入调试控制台。打开控制台，在控制台窗口中键入 window.performance.timing。从输出窗口可以看出，window.performance 返回的性能对象也包含计时属性。 [![cross browser testing](img/af15d82b1515952832bbd806742eea4a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---P5v_9IQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/03/image7-4-1.png)

关于导航定时 API 和相应浏览器兼容性的更多细节可以在[这里](https://developer.mozilla.org/en-US/docs/Web/API/Navigation_timing_API)找到。

***嘿你是不是在找一个免费的工具来有效的生成随机词。你可以通过[随机字生成器](https://www.lambdatest.com/free-online-tools/random-word-generator?utm_source=devto&utm_medium=organic&utm_campaign=jul27_rn&utm_term=rn&utm_content=free_tools)工具来实现。***

## 用 Selenium 自动化测试来测量页面加载时间

您可以利用 Selenium 来测量一段时间内 web 页面的性能。从导航计时 API 获得的结果可以进一步用于改进 Selenium 测试的页面加载时间，并且还可以将这些结果与竞争对手的 web 性能进行比较。虽然两个网站/网络应用提供的主页和功能会有明显的差异(解决类似的问题)，但页面加载速度与你的竞争对手相等(如果不是更好的话)仍然很重要。

除了跟踪和基准测试 web 页面的性能，Selenium 还可以用来记录页面的统计数据，以便在客户端进行改进。就服务器端而言，通过使用合适的基础设施，可以获得较大的性能提升。

### 示例 1–使用导航计时 API 捕获网页计时的统计数据

让我们看一个例子，在这个例子中，Python 和 Selenium 框架一起被用来捕获关于 web 页面的重要统计信息(例如 lambdatest.com)。这是一个示例，您可以使用它开始测量 Selenium 中的页面加载时间。

```
''' Loosely based on the example code in http://www.obeythetestinggoat.com/
how-to-get-selenium-to-wait-for-page-load-after-a-click.html
'''
'''
Import the necessary packages required for execution
'''
from selenium import webdriver

''' Chrome web driver interface
'''
hyperlink = "http://lambdatest.com"
driver = webdriver.Chrome()
driver.get(hyperlink)

''' Use Navigation Timing  API to calculate the timings that matter the most '''   

navigationStart = driver.execute_script("return window.performance.timing.navigationStart")
responseStart = driver.execute_script("return window.performance.timing.responseStart")
domComplete = driver.execute_script("return window.performance.timing.domComplete")

''' Calculate the performance'''
backendPerformance_calc = responseStart - navigationStart
frontendPerformance_calc = domComplete - responseStart

print("Back End: %s" % backendPerformance_calc)
print("Front End: %s" % frontendPerformance_calc)

driver.quit() 
```

Enter fullscreen mode Exit fullscreen mode

首先，创建一个 Chrome web-driver 实例，并在浏览器中打开测试中的网页。我们利用 Performance Timing API 来获取从浏览器发出请求开始每个事件所花费的时间。execute_script 选项用于在当前窗口/框架中同步执行 JavaScript 代码。

对于演示，我们使用了以下指标:

*   **navigation start**–该属性返回用户代理完成卸载前一个页面/文档所花费的时间。如果在加载新页面之前没有文档，navigationStart 将返回与 fetchStart 相同的值。
*   **response start**–该属性在用户代理从服务器或本地源/应用程序缓存接收到第一个字节时立即返回时间。
*   **DOM complete**–该属性返回当前文档/页面就绪设置为“完成”之前的时间。document.readyState 状态为“完成”表示页面/文档的解析已经完成&页面所需的所有资源都已下载。在接下来的章节中，我们将会看到一个 domComplete 的例子。

window . performance . Timing . Navigation start、window . performance . Timing . response start 和 window . performance . Timing . DOM complete 是计时导航选项，它们作为参数传递给 execute_script() API。时间的度量单位是毫秒(ms)。

我们使用 Eclipse 作为测试代码开发和测试的 IDE。为了执行代码，在 Eclipse IDE 中按(CTRL + F9)。下面是执行上述代码后的输出，输出显示了网页前端和后端活动所花费的时间。[![Navigation Timing API](img/e2e9a75e9f588188410f524534038296.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zHNLefrq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/03/image1-5.png)

### 示例 2–使用 WebDriverWait 接口在网页上搜索元素

我们来看另一个例子，在这个例子中，我们加载一个 web 页面，并在页面中搜索元素(div-id)的存在。我们搜索超时持续时间为 5 秒的 div-id。一旦页面被加载，div-id(在我们的例子中是:owl-example)将被搜索 5 秒钟，之后，如果 owl-example div-id 不在 web 页面上，就会引发一个异常。所有必需的模块都在实现开始时导入。

```
from selenium import webdriver
from selenium.common.exceptions import TimeoutException
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By

''' Create a Chrome webdriver instance''' 
driver = webdriver.Chrome()
#driver = webdriver.Firefox()

''' Open the webpage under test'''
driver.get('http://lambdatest.com')
timeout = 5

''''' Test case 1 - The required div-id is not present on the web-page ''''' 
#while True:
try:
    element_present = EC.presence_of_element_located((By.ID, 'owl-example-1'))
    WebDriverWait(driver, timeout).until(element_present)
    print("1 - Element is present on the page")
#        break
except TimeoutException as ex:
    print("1 - Timed out waiting for page to load " + str(ex))
#        break

''''' Test case 2 - The required div-id is not present on the web-page '''''
#while True:
try:
    element_present = EC.presence_of_element_located((By.ID, 'owl-example'))
    WebDriverWait(driver, timeout).until(element_present)
    print("2 - Element is present on the page")
#        break
except TimeoutException as ex:
    print("2 - Timed out waiting for page to load " + str(ex))
#        break

''' Free up the resources'''
driver.close()
driver.quit() 
```

Enter fullscreen mode Exit fullscreen mode

像前面的例子一样，我们初始化 Chrome webdriver 接口。测试中的网页是 LambdaTest 主页——lambdatest.com。超时持续时间设置为 5 秒。有两个测试用例，其细节将在下面提到

*   **测试用例 1**–检查正在加载的网页是否存在 div-id“owl-example-1”。使用 WebDriverWait 接口，搜索将执行 5 秒钟。如果页面上没有“owl-example-1 ”,就会引发异常。
*   **测试用例 2**–测试条件与测试用例 1 相同，除了搜索下的 div-id 是‘owl-example’

下面是上例 [![](img/b1210b7453f1dba913d9bf0f927bd8ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3_QFqQo4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/03/image2-7.png) 的输出

嘿，你正在寻找一个免费的工具来转换你的二进制到十进制。你可以通过 [RC32 哈希计算器](https://www.lambdatest.com/free-online-tools/rc32-hash-calculator?utm_source=devto&utm_medium=organic&utm_campaign=jul27_rn&utm_term=rn&utm_content=free_tools)工具来完成。

正如所料，第一个测试用例失败了，因为被搜索的 div-id 不在被测试的 web 页面上。搜索在 5 秒钟后超时。另一方面，第二个测试用例通过了，因为 div-id‘owl-example’出现在网页上。如需确认，请访问 [LambdaTest](https://www.lambdatest.com/?utm_source=Dev&utm_medium=Blog&utm_campaign=hi-05-200219us&utm_term=OrganicPosting) 的 HTML 源代码。 [![lambdatest](img/6b9215237ee5f7d16010af0b052620c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vj5Gdakt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/03/image4-3.png) (也就是被测试的页面)

有些情况下，你需要在网页加载时执行操作，即 document.readyState 状态为“完成”。该操作可以是点击超链接或点击按钮或任何其他操作。这可能是一个棘手的情况，因为可能会出现这样的场景，在客户端使用 AJAX(异步 JavaScript 和 XML)异步加载网页的几个部分。我们关注网页操作，即加载新的网页。在 Selenium 测试教程的这一节中，我们来看看 Selenium 用来检查整个网页是否被加载的机制。

### 示例 3–用 pytest & Selenium 检测页面加载完成

Selenium 有一个称为 staleness _ of 的内置条件和自己的 wait_for 实现。staleness _ of 是 selenium 类 selenium . web driver . support . expected _ conditions 的元素。staleness _ of 将一直等到一个元素没有附加到 DOM。下面是
类的实现

```
class selenium.webdriver.support.expected_conditions.staleness_of(element) 
```

Enter fullscreen mode Exit fullscreen mode

元素是网页上要等待的实际元素。如果元素附加到 DOM，则返回 False，否则返回 True。我们将使用 pytest 框架，我们在之前的博客中已经介绍过了。在下面的例子中，使用了@contextmanager 来帮助在需要时分配和释放必要的资源。

```
''' Import the 'modules' that are required for execution '''
''' In this example, we make use of pytest framework along with Selenium '''
import pytest
import pytest_html
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.keys import Keys
from time import sleep
from contextlib import contextmanager
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support.expected_conditions import \
    staleness_of

@pytest.fixture(params=["chrome"],scope="class")
def driver_init(request):
    if request.param == "chrome":
        web_driver = webdriver.Chrome()
    request.cls.driver = web_driver
    yield
    web_driver.close()

@pytest.mark.usefixtures("driver_init")
class BasicTest:
    pass
class Test_URL(BasicTest):
        def test_open_url(self):
            self.driver.get("https://www.lambdatest.com/")
            print(self.driver.title)
            sleep(5)

        @contextmanager
        def wait_for_page_load(self, timeout=30):
            #Search for the div-id owl-example
            old_page = self.driver.find_element_by_id('owl-example')  
            yield
            WebDriverWait(self.driver, timeout).until(
                staleness_of(old_page)
            )

        def test_click_operation(self):
            # Wait for a timeout duration of 10 seconds, after which we perform a CLICK operation
            with self.wait_for_page_load(timeout=10):
                self.driver.find_element_by_link_text('FREE SIGN UP').click()
                print(self.driver.execute_script("return document.readyState")) 
```

Enter fullscreen mode Exit fullscreen mode

在 wait_for_page_load()中，搜索 div-id owl-example。WebDriverWait 与 ExpectedCondition 结合使用，以实现显式等待条件。驱动程序和超时作为参数传递。我们一直等到元素失效。如果被搜索的元素(即 owl-exampl '不存在),则会引发 TimeoutException。这就是我们如何检查元素的陈旧性。

在 test_click_operation()中，加载网页 lambdatest.com，然后等待最长 10 秒钟。它使用 wait_for_page_load()来检查网页上的元素 owl-example 是否过期。一旦网页加载超时，我们检查是否有一个元素有链接文本免费注册，这是一个按钮，超链接到注册页面。最后，我们使用 Document.readyState 属性检查 web 页面/文档的状态。当该特定属性的值更改时，将在 document 对象上触发 readystatechange 事件。

三种可能的状态是加载、交互和完成。如果网页/文档及其相关资源已完成加载，Document.readyState 将达到完成状态。在我们的例子中，如果页面被完全加载，则执行一个按钮点击，最终将我们带到 LambdaTest 注册页面 [![cross browser testing](img/9e85dab526394b18f06fffea39562581.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--taLukQ1f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/03/image6-2.png)

执行时，使用 py.test 命令，启用 verbose 并将–capture 设置为 no [![image5 (4)](img/381f1fbd7ce25cef34ee8ee154ccb7ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yW6pN6Fp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/03/image5-4.png)

根据目标市场和负责给你带来最大流量的浏览器，你应该创建一个[跨浏览器测试矩阵](https://www.lambdatest.com/blog/creating-browser-compatibility-matrix-for-testing-workflow/?utm_source=Dev&utm_medium=Blog&utm_campaign=hi-05-200219us&utm_term=OrganicPosting)来[有组织地执行跨浏览器测试](https://www.lambdatest.com/blog/performing-cross-browser-testing-with-lambdatest/?utm_source=Dev&utm_medium=Blog&utm_campaign=hi-05-200219us&utm_term=OrganicPosting)。对你的产品进行跨浏览器测试将有助于你发现有价值的测试用例，这些用例可以进一步用于改善页面加载时间。您可以利用 LambdaTest，而不是创建一个本地基础设施来进行这些测试，LambdaTest 在云上提供了可伸缩的跨浏览器测试工具。

## 结论

测量页面加载时间对于基于 web 的产品非常重要，因为它直接影响用户体验。除了优化产品的整体设计，以尽量缩短页面加载时间的方式设计网页也很重要。这将提高产品的预期转化率。

导航计时 API 有助于测量页面加载时间。您可以将这些 API 生成的输出用于优化目的。窗户。performance 返回 performance 对象，该对象提供有关当前文档/页面的重要信息。它可以与 Selenium 框架一起使用。使用 Selenium 测量页面加载时间至关重要，因为不同类型和版本的浏览器的网页会有所不同，因此重点优化您的产品在浏览器和平台上的页面速度以获得卓越的客户体验非常重要。

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://accounts.lambdatest.com/register/?utm_source=Dev&utm_medium=Blog&utm_campaign=hi-05-200219us&utm_term=OrganicPosting)

原文出处:[lambdatest.com](https://www.lambdatest.com/blog/how-to-measure-page-load-times-with-selenium/?utm_source=Dev&utm_medium=Blog&utm_campaign=hi-05-200219us&utm_term=OrganicPosting)

**相关帖子**

1.  [专业测试人员如何在 Selenium 自动化脚本中使用 CSS 选择器？](https://www.lambdatest.com/blog/how-pro-testers-use-css-selectors-in-selenium-automation-scripts/?utm_source=Dev&utm_medium=Blog&utm_campaign=hi-05-200219us&utm_term=OrganicPosting)
2.  [自动测试硒，黄瓜&测试 NG](https://www.lambdatest.com/blog/automation-testing-with-selenium-cucumber-testng/?utm_source=Dev&utm_medium=Blog&utm_campaign=hi-05-200219us&utm_term=OrganicPosting)
3.  [使用 TestNG 加速 Selenium 中的自动化并行测试](https://www.lambdatest.com/blog/speed-up-automated-parallel-testing-in-selenium-with-testng/?utm_source=Dev&utm_medium=Blog&utm_campaign=hi-05-200219us&utm_term=OrganicPosting)