# 使用 Python 中的 Selenium 自动获取网站截图

> 原文：<https://dev.to/bk/website-screenshots-with-selenium-in-python-kcn>

## 更新 09/11/2020

为更加清晰起见，更新了标题
，发布于[https://bilalkhoukhi . com/blog/automate-taking-website-screen-with-selenium-in-python](https://bilalkhoukhi.com/blog/automate-taking-website-screenshots-with-selenium-in-python)

## 更新 01/01/2019

这里有一个 Node.js 应用程序做同样的工作，它使用 React 前端。 [Node.js App 与 Selenium 和 React](https://github.com/Bilal-io/Web_Screen_Shooter-React-Selenium)

## 更新 2018 年 12 月 26 日

如果你想在 Windows、Mac、Linux(桌面或通过 SSH)上使用 Headless Chrome 来运行这项工作，我在文章的末尾添加了一个新的部分。

## 原帖

简而言之，我害怕手工做重复的任务，这就是为什么我宁愿花几分钟(第一次做时几个小时)来写一个脚本来自动完成这个过程。否则我无法集中注意力，如果你也像我一样不擅长集中注意力，我推荐你读读加州纽波特的《深度工作》。

### 问题:

我有一个笔记本电脑，stablet 和手机的 Photoshop 模型，我需要用来展示一个网站。

[![Image](img/685dcd1192fe30e49b2ad71c7af5fa42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6kOIlAg---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/arxj243bn90hu4z27zbs.jpg)

这是一个简单快捷的工作，3 个截图和 2 分钟后，你就完成了。但是，如果你有 10 个、20 个或 100 个网站，并且你必须提供不同屏幕尺寸(一个桌面、一个移动和一个平板)的截图，那该怎么办呢？

### 解:

硒是解决办法。由于我只在 Python 中使用过它，所以我选择用 Python 来完成本教程，与 Node.js 项目相比，它的设置速度更快，但功能应该是相似的。

让我们开始:

如果你的系统中还没有安装 Python，可以从这里获得: [Python 3.6+](https://www.python.org/downloads/)
你还需要 [Chrome Webdriver](http://chromedriver.chromium.org/downloads)

Python 安装完成后，运行命令:
安装 Selenium

```
$ pip install selenium 
```

Enter fullscreen mode Exit fullscreen mode

python 很容易上手，你所需要的只是一个文件，它导入了你将要使用的模块，`selenium`或许还有`time`，这将允许我们在页面加载之后，截屏之前延迟/等待几秒钟。

我们还将利用`time`模块，它是 [Python 标准库的一部分。](https://docs.python.org/3/library/time.html)。

接下来，创建一个扩展名为`.py`的文件，用您喜欢的编辑器打开它。

首先，我们将从 selenium 和 time 导入 webdriver 开始。

```
from selenium import webdriver
import time 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们定义一个我们想要截图的链接列表

```
links = ['dev.to', 'twitter.com', 'github.com'] 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，网络驱动不认为上面的链接是有效的，因为它们没有`https://`协议。我们稍后会添加它，我添加它们而不添加协议的原因是，我可以使用上面的文件名。

我们可以通过如下调用`webdrive.chrome(/path/to/chromedriver)`开始使用 web driver

```
with webdriver.Chrome("C:/chromedriver_win32/chromedriver") as driver:
    # code goes here using driver 
```

Enter fullscreen mode Exit fullscreen mode

以上读作`with this function defined as driver.. do things with driver`
，有点等同于

```
driver = webdriver.Chrome("C:/chromedriver_win32/chromedriver")
# code goes here using driver 
```

Enter fullscreen mode Exit fullscreen mode

据我所知，区别在于第一种方法在完成工作后会自动关闭浏览器，不像第二种方法，你必须显式调用`driver.close()`来关闭浏览器。如果有人知道其他原因，请在评论里告诉我。

对于本教程，我将使用第一种方法。对于 Python 的新手来说，需要注意的是，下面几行是缩进的，因为它们包含在上面的语句中，类似于在 C++、JavaScript 和其他语言中使用花括号来包含函数/类体。

为了截屏，我们将使用 3 个命令:`set_window_size()`、`get()`和`save_screenshot()`、T3

```
driver.set_window_size(width, height) # takes two arguments, width and height of the browser and it has to be called before using get() driver.get(url) # takes one argument, which is the url of the website you want to open driver.save_screenshot(/output-dir/file_name.png) # this one takes one argument which is the path and filename all concatenated. Important: the filename should end with .png 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于这些命令的信息，请阅读 [Webdriver API 文档](https://selenium-python.readthedocs.io/api.html)

让我们把它们放在一起，我们将需要遍历我们上面创建的链接列表，并定义一些变量用于文件名、宽度和高度，以及使用`https://`协议
的链接

```
with webdriver.Chrome("C:/chromedriver_win32/chromedriver") as driver:
    for link in links:
        desktop = {'output': str(link) + '-desktop.png',
                   'width': 2200,
                   'height': 1800}
        tablet = {'output': str(link) + '-tablet.png',
                  'width': 1200,
                  'height': 1400}
        mobile = {'output': str(link) + '-mobile.png',
                  'width': 680,
                  'height': 1200}
        linkWithProtocol = 'https://' + str(link) 
```

Enter fullscreen mode Exit fullscreen mode

我们已经定义了 3 个字典，包含我们需要的每个屏幕尺寸的输出文件名，宽度和高度，你可以根据你的需要改变尺寸。

现在我们可以开始使用上面解释的命令

```
 # set the window size for desktop
        driver.set_window_size(desktop['width'], desktop['height'])
        driver.get(linkWithProtocol)
        time.sleep(s)
        driver.save_screenshot(desktop['output']) 
```

Enter fullscreen mode Exit fullscreen mode

注意到我们使用了`time.sleep(1)`，这意味着我们要求 python 延迟下一行的执行，直到 2 秒过去。即使没有`sleep`命令，驱动程序也会在截图之前等待页面完全加载，然而，如果你有任何动画，你可能希望等待事情稳定下来，如果不需要，那么你可以摆脱线。

这就是平板电脑和手机的样子:

```
 # set the window size for tablet
        driver.set_window_size(tablet['width'], tablet['height'])
        driver.get(linkWithProtocol)
        time.sleep(2)
        driver.save_screenshot(tablet['output'])

        # set the window size for mobile
        driver.set_window_size(mobile['width'], mobile['height'])
        driver.get(linkWithProtocol)
        time.sleep(2)
        driver.save_screenshot(mobile['output']) 
```

Enter fullscreen mode Exit fullscreen mode

这样，你需要做的就是从命令行运行脚本

```
python script.py 
```

Enter fullscreen mode Exit fullscreen mode

这将打开 Chrome 浏览器，调整它的大小，每次截屏并在你拥有 script.py 的同一个目录下输出文件，直到完成。

完整的代码可以在 Github 上找到

### 终注

Selenium 可以做很多事情，它可以用 CSS 和 JavaScript 操作页面，填写表单并提交它们，等等...学会它并善用它。

## 无头铬如何使用硒

当你试图完成其他工作时，浏览器打开和关闭如此之多可能会很烦人。解决办法是使用无头 Chrome。

上面的代码不会有太大的变化。
我们需要定义我们的`ChromeOptions()`，并使用`add_argument()`命令给它添加一个参数，如下所示:

```
options = webdriver.ChromeOptions() # define options options.add_argument("headless") # pass headless argument to the options 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将向之前定义的命令传递一个新参数:

```
with webdriver.Chrome("C:/chromedriver_win32/chromedriver") as driver:
    # code here 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
with webdriver.Chrome("C:/chromedriver_win32/chromedriver", chrome_options=options) as driver:
    # code here 
```

Enter fullscreen mode Exit fullscreen mode

这是我测试过的 Mac 和 Windows 所需要的。Linux 桌面还没有经过测试，但我认为它也能工作。

### Linux 服务器通过 SSH

Linux 桌面和服务器之间的工作方式是不同的，后者没有屏幕，我确信它已经去掉了许多它不需要的驱动程序，因此，我们必须再传递一些参数才能让它工作。由于我没有在 Linux 桌面上测试过，如果上面的解决方案不工作，那么就像对待 Linux 服务器一样对待它

首先安装 Chrome 浏览器(我从没想过我会在这里用到它)

```
sudo apt-get install -y chromium-browser # BTW I use Ubuntu (says a non-Arch user) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们添加这些参数

```
options = webdriver.ChromeOptions() # define options options.add_argument("headless") # pass headless argument to the options options.binary_location = '/usr/bin/chromium-browser' # location of the Chrome Browser binary options.add_argument("disable-infobars") # disabling infobars options.add_argument("--disable-extensions") # disabling extensions options.add_argument("--disable-gpu") # applicable to windows os only options.add_argument("--disable-dev-shm-usage") # overcome limited resource problems options.add_argument("--no-sandbox") # Bypass OS security model
# thanks to https://stackoverflow.com/a/50642913/2291648, this answer helped debug the issue 
```

Enter fullscreen mode Exit fullscreen mode

就像我们上面做的一样，将`chrome_options=options`传递给`webdriver.Chrome(...,webdriver.Chrome)`作为第二个参数。

这对我有用，我希望对你也有用。

### [【Github 链接(更新回购)](https://github.com/Bilal-io/Selenium-Screenshot-Script)