# 建立一个 Python 机器人来找到你的网站的死链接

> 原文：<https://dev.to/healeycodes/build-a-python-bot-to-find-your-website-s-dead-links-563c>

死链接和图像，让游客感到沮丧。手动检查它们会更加令人沮丧！我们将构建一个机器人，仅使用 Python 标准库来抓取网站上缺失的资源。

[![](img/dd1b84e5102efb5c528236cbcd712338.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5dCEYOws--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uk31t1u3c6l4xcsdivoz.gif)

我们来谈谈设计目标。我们想运行一个命令，让整个网站检查死资源。这意味着将涉及一些爬行。

```
$ python deadseeker.py 'https://healeycodes.com/'
> 404 - https://docs.python.org/3/library/missing.html
> 404 - https://github.com/microsoft/solitare2 
```

Enter fullscreen mode Exit fullscreen mode

从技术上讲，机器人应该解析给定页面上的所有 HTML 标签，寻找`href`和`src`属性。如果发现任何错误，它应该发送一个 GET 请求并记录任何 HTTP 错误代码。如果它发现本地页面(如`/about/`、`/projects/`)，它应该将它们排队以供以后扫描。当我们检查链接时，让我们将它们添加到一个集合中，这样我们只检查它们一次。

Python 为我们提供了[html . parser](https://docs.python.org/3/library/html.parser.html#module-html.parser)——一个简单的 HTML 和 XHTML 解析器。让我们来看看它是如何工作的。

```
from html.parser import HTMLParser

# extend HTMLParser class MyHTMLParser(HTMLParser):
    # override `handle_starttag`
    def handle_starttag(self, tag, attrs):
        print(f'Encountered a start tag: {tag}')
        print(f'And some attributes: {attrs}')

parser = MyHTMLParser()
parser.feed('<html><body><a href="https://google.com">Google</a></body></html>') 
```

Enter fullscreen mode Exit fullscreen mode

这张照片:

```
> Encountered a start tag: a
> And some attributes: [('href', 'https://google.com')] 
```

Enter fullscreen mode Exit fullscreen mode

那是为我们处理的重物。请求怎么样了？Python 有 [urllib.request](https://docs.python.org/3/library/urllib.request.html#module-urllib.request) 。它有`urllib.request.urlopen`,我们将使用它来发送 GET 请求。大多数时候你会使用第三方库[请求](http://docs.python-requests.org)，但是我们的需求很小，我们可以简单地实现它！

让我们检查一下 Google 是否启动了，我们期待一个 HTTP 状态代码 200 (OK)。

```
>>> import urllib.request
>>> r = urllib.request.urlopen('https://google.com')
>>> r.status
200 
```

Enter fullscreen mode Exit fullscreen mode

有些网站会返回 403(禁止)，因为我们的用户代理会背叛我们这个机器人。默认情况下，它看起来会像`User-Agent: Python-urllib/3.7`。我们可以通过伪装成不同的用户代理来解决这个问题。负责任的机器人会先检查 [robots.txt](http://www.robotstxt.org/) 检查网站的规则！

我们从进口开始，获取我们需要的一切。我们还存储了对用户代理字符串的引用。这意味着*用户正在浏览最近的 Chrome 版本*。

```
import sys
import urllib
from urllib import request, parse
from urllib.parse import urlparse, urljoin
from urllib.request import Request
from html.parser import HTMLParser
from collections import deque

search_attrs = set(['href', 'src'])
agent = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36' 
```

Enter fullscreen mode Exit fullscreen mode

我们还导入了一个数据结构，并声明了另一个数据结构——dequee 和 set。 [deque](https://docs.python.org/3/library/collections.html#collections.deque) 是一个“类似列表的容器，具有快速追加和两端弹出功能”。我们将把它作为一个简单的队列——在发现本地页面时添加它们，并以先进先出的方式扫描它们。我们的 [Set](https://docs.python.org/3/library/stdtypes.html#set) 用法更简单，我们将在发送和添加链接之前检查我们是否已经向链接发送了请求。在这两种情况下，我们都可以使用一个[列表](https://docs.python.org/3/tutorial/datastructures.html)，但是这样会降低计算效率。

我们将`HTMLParser`扩展为`LinkParser`——我们程序的核心。我们使用 [super()](https://docs.python.org/3/library/functions.html#super) 来引用我们正在覆盖的父构造函数。

```
class LinkParser(HTMLParser):
    def __init__(self, home):
        super().__init__()
        self.home = home
        self.checked_links = set()
        self.pages_to_check = deque()
        self.pages_to_check.appendleft(home)
        self.scanner() 
```

Enter fullscreen mode Exit fullscreen mode

当我们创建这个类的实例时，我们把它传递给我们网站的主页。我们将它存储为`self.home`,这样我们可以用它来检查我们遇到的链接是否是本地页面。正如你在构造函数的末尾看到的，我们马上开始扫描——但是“扫描”是什么意思呢？

```
def scanner(self):
    # as long as we still have pages to parse
    while self.pages_to_check:

        # take the first page added
        page = self.pages_to_check.pop()

        # send a request to it using our custom header
        req = Request(page, headers={'User-Agent': agent})
        res = request.urlopen(req)

        # check that we're about to parse HTML (e.g. not CSS)
        if 'html' in res.headers['content-type']:
            with res as f:

                # read the HTML and assume that it's UTF-8
                body = f.read().decode('utf-8', errors='ignore')
                self.feed(body) 
```

Enter fullscreen mode Exit fullscreen mode

当`feed`解析 HTML 时，它会遇到标签并调用`handle_starttag`、`handle_endtag`和其他方法。我们用自己的方法覆盖了`handle_starttag`，该方法检查我们正在寻找的键的属性。当我们遇到`<a href="http://google.com">Google</a>`时，我们想要提取`href`值。类似地，对于`<img src="/cute_dog.png">`,我们需要`src`值。

```
def handle_starttag(self, tag, attrs):
    for attr in attrs:
        # ('href', 'https://google.com')
        if attr[0] in search_attrs and attr[1] not in self.checked_links:
            self.checked_links.add(attr[1]) 
            self.handle_link(attr[1]) 
```

Enter fullscreen mode Exit fullscreen mode

提醒:要循环遍历一个 iterable，可以使用`for thing in things:`，在下面的块中，通过第一个变量`thing`引用每一项。要检查某个东西是否在集合中，可以使用`item in a_set`，它返回一个布尔值。可以用`a_set.add(item)`加。

```
def handle_link(self, link):
    # check for a relative link (e.g. /about/, /blog/)
    if not bool(urlparse(link).netloc):

        # fix if we need to, we can't send a request to `/about/`
        link = urljoin(self.home, link)

    # attempt to send a request, seeking the HTTP status code
    try:
        req = Request(link, headers={'User-Agent': agent})
        status = request.urlopen(req).getcode()

    # we're expecting errors (dead resources) so let's handle them
    except urllib.error.HTTPError as e:
        print(f'HTTPError: {e.code} - {link}')  # (e.g. 404, 501, etc)
    except urllib.error.URLError as e:
        print(f'URLError: {e.reason} - {link}')  # (e.g. conn. refused) 
    # otherwise, we got a 200 (OK) or similar code!
    else:

        # remove this in production or we won't spot our errors
        print(f'{status} - {link}')

    # build a queue of local pages so we crawl the entire website
    if self.home in link:
        self.pages_to_check.appendleft(link) 
```

Enter fullscreen mode Exit fullscreen mode

一个完整的死资源爬虫，不到 50 行普通代码。赞 Python。许多人认为扩展的标准库是它受欢迎的原因之一。我们需要做的最后一件事是调用我们的类，在脚本之后给它传递第一个参数。

```
LinkParser(sys.argv[1])  # e.g. 'https://healeycodes.com/' 
```

Enter fullscreen mode Exit fullscreen mode

这里是包含最终代码的[库](https://github.com/healeycodes/Broken-Link-Crawler)。教程评论已经清理完毕。快乐装瓶😀

* * *

我在我的每周[简讯](https://buttondown.email/healeycodes)上发布独特的内容📧。