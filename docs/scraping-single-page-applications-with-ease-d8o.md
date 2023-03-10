# 轻松抓取单页应用程序。

> 原文：<https://dev.to/scrapingbee/scraping-single-page-applications-with-ease-d8o>

处理一个使用大量 Javascript 来呈现内容的网站可能会很棘手。如今，越来越多的网站使用 Angular，React，Vue.js 这样的框架作为他们的前端。

这些前端框架处理起来很复杂，因为经常使用 HTML5 API 的最新特性。

所以基本上你会遇到的问题是，你的无头浏览器会下载 HTML 代码，和 Javascript 代码，但不能执行完整的 Javascript 代码，网页不会完全呈现。

这些问题有一些解决方法。第一种是使用更好的无头浏览器。第二个是检查 Javascript 前端发出的 API 调用并再现它们。

收集这些 spa 可能很有挑战性，因为通常涉及到大量的 Ajax 调用和 Websockets 连接。如果性能是一个问题，您应该总是尝试再现 Javascript 代码，这意味着用浏览器检查器手动检查所有网络调用，并复制包含有趣数据的 AJAX 调用。

所以取决于你想做什么，有几种方法可以刮这些网站。例如，如果您需要截屏，您将需要一个真正的浏览器，能够解释和执行所有的 Javascript 代码，以便呈现页面，这就是下一部分的内容。

# 无头铬带蟒

PhantomJS 是这个领域的领导者，它过去(现在仍然)大量用于浏览器自动化和测试。在听到 Chrome 发布无头模式的消息后，PhantomJS 维护者说他将不再担任维护者，因为我引用“Google Chrome 比 PhantomJS 更快、更稳定。”...]“看起来无头模式的 Chrome 正在成为浏览器自动化和处理大量 Javascript 网站的一种方式。

# 先决条件

您需要安装 selenium 包:

```
pip install selenium 
```

Enter fullscreen mode Exit fullscreen mode

当然，你需要一个 Chrome 浏览器，并且在你的系统上安装 Chromedriver。

在 macOS 上，你可以简单地使用 brew:

```
brew install chromedriver 
```

Enter fullscreen mode Exit fullscreen mode

# 截图

我们将使用 Chrome 截取任天堂主页的截图，其中使用了大量的 Javascript。

```
> chrome.py

from selenium import webdriver
from selenium.webdriver.chrome.options import Options

options = Options()
options.headless = True
options.add_argument("--window-size=1920,1200")

driver = webdriver.Chrome(options=options, executable_path=r'/usr/local/bin/chromedriver')
driver.get("https://www.nintendo.com/")
driver.save_screenshot('screenshot.png')
driver.quit() 
```

Enter fullscreen mode Exit fullscreen mode

代码非常简单，我只是添加了一个参数-窗口大小，因为默认大小太小了。

你现在应该有一个任天堂主页的漂亮截图了:

[![Nintendo Homepage Screenshot](img/30675f43abbab2e726207046a8d245ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bf00LEFQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rpui3447ha74z56t6vkb.png)

# 等待页面加载

大多数情况下，一个页面上会触发大量的 AJAX 调用，您必须等待这些调用加载才能获得完全呈现的页面。

解决这个问题的一个简单方法是将 time.sleep()设置为任意长的时间。这种方法的问题是，根据您的等待时间和互联网连接速度，您要么等待的时间太长，要么等待的时间太少。

另一个解决方案是使用 Selenium API 中的 WebDriverWait 对象:

```
try:

 elem = WebDriverWait(driver, delay)
     .until(EC.presence_of_element_located((By.NAME, 'chart')))

 print("Page is ready!")

except TimeoutException:

 print("Timeout") 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很好的解决方案，因为它将等待元素在页面上呈现所需的确切时间。

# 结论

如你所见，在 Python 中设置无头模式的 Chrome 非常简单。最具挑战性的部分是在生产中管理它。如果你抓取了很多不同的网站，资源的使用是不稳定的。

这意味着会出现 CPU 峰值和内存峰值，就像普通的 Chrome 浏览器一样。毕竟，你的 Chrome 实例将执行不可信和不可预测的第三方 Javascript 代码！此外还有僵尸进程的问题

这是我开始 [ScrapingBee](https://https://www.scrapingbee.com) 的原因之一，这样开发者就可以专注于提取他们想要的数据，而不是管理无头浏览器和代理！

这是我关于刮擦的第一篇文章，我希望你喜欢！

如果你确实让我知道，我会写更多😊

如果你想了解更多关于 ScrapingBee 的信息，你可以👉[此处](https://dev.to/daolf/new-season-new-project-i-need-you-197l)