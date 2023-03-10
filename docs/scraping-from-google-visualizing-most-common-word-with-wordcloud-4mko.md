# 从谷歌抓取&用 Wordcloud 可视化最常见的单词

> 原文：<https://dev.to/muhajirdev/scraping-from-google-visualizing-most-common-word-with-wordcloud-4mko>

你试过在谷歌上搜索你的名字吗？还是别人的名字？与你的名字相关的最常见的单词是什么？在本教程中，我们将了解:)

要求:

*   计算机编程语言
*   互联网连接:)

在本教程中，我们有两个部分。`Scraping`和`Visualizing`

# 刮削

## 安装 Scrapy:

```
pip install scrapy 
```

Enter fullscreen mode Exit fullscreen mode

## 初次尝试

让我们创建我们的项目。

```
scrapy startproject google
cd google 
```

Enter fullscreen mode Exit fullscreen mode

让我们试着在谷歌上搜索你的名字。
我假设你的名字是`Sukijan`。

## 获取所有文本

让我们尝试从一个 url 获取所有文本。我试试这个网址`https://www.aljazeera.com/news/2019/06/israeli-forces-settlers-enter-al-aqsa-mosque-compound-190602065712978.html`(我今天早上得到的第一条消息之一)

创建一个文件`google/google/spiders/test.py`

```
import scrapy

class TestSpider(scrapy.Spider):
    name = 'test'

    # Tell scrapy, we want to start crawing with these pages
    start_urls = [
        'https://www.aljazeera.com/news/2019/06/israeli-forces-settlers-enter-al-aqsa-mosque-compound-190602065712978.html']

    # What we're going to do after the page is resolved
    def parse(self, response):
        # We yield a dict of this
        yield {
            # response.css is css style selector.
            # ::text means we want to to get the text. If we don't put ::text
            # We'll get Israeli forces and settlers ...
            # When we add text we got 'Israeli forces and settlers ...'
            'title': response.css('title::text').get(),
            # Same thing with `p`, We're selecting the text of element p
            # But notice that we use `getall` instead of `get`
            # `get` will select only one element.
            # While `getall`  Will select all element, and return list
            'text': response.css('p::text').getall()
        } 
```

Enter fullscreen mode Exit fullscreen mode

现在试着跑一下`scrapy crawl test`。您将在命令行中看到结果。

让我们试着用`scrapy crawl test -o result.json`将结果写入一个文件

现在你可以在`result.json`中看到 JSON 格式的结果

## 从谷歌获取链接

让我们尝试从谷歌抓取许多网址

首先。转到`settings.py`
并确认

```
...
ROBOTSTXT_OBEY = False
... 
```

Enter fullscreen mode Exit fullscreen mode

```
import scrapy
import re

def is_google(a):
    url = a.attrib['href']
    return bool(re.search("/search", url))

class TestSpider(scrapy.Spider):
    name = "test"

    # How to get the start url
    # search `sukijan` on Google. And then go to second page, and then click first page result. You'll see that the url now includes `start` parameter. We'll use that for pagination
    # Also pay attention to parameter `q=sukijan`. That's our keyword
    # We use `% start for start in range(0, 100, 10)`. To generate urls that we want to crawl
    # So it will generate a list of urls like so
    # [q=sukijan start =0, q=sukijan start=10, q=sukijan start=20, q=sukijan, start=30... until 100] 
    start_urls = [
        'https://www.google.com/search?q=sukijan&safe=strict&ei=sLTvXL_KH4zfz7sPpZOBuA4&start=%s&sa=N&ved=0ahUKEwi_4un2icPiAhWM73MBHaVJAOc4ChDx0wMIjQE&cshid=1559213273144254&biw=1680&bih=916' % start for start in range(0, 100, 10)
    ]

    def parse(self, response):
        # What is `jfp3ef a`
        # If you do `scrapy shell https://www.google.com/search?q=Sukijan&oq=sukijan&aqs=chrome.0.69i59j0l5.1739j0j7&sourceid=chrome&ie=UTF-8#ip=1`
        # And then do `view(response)`. It will open up a browser
        # From there do inspect element, locate a link, And you'll find that most of the links fall under `.jfp3ef` class
        for href in response.css('.jfp3ef a'):
            # We want to open url these links. But we don't want to open Google's url
            # For example url `More images for Sukijan`, etc.
            if not is_google(href):
                # This basically means 'Hey scrapy` follow this url.
                # When you find it run parse_text function on it.
                yield response.follow(href, self.parse_text)

    def parse_text(self, response):

        # get_text takes the response
        # And then it will takes all text whether it's in <p> tag, <h1> tag, <h2> tag etc
        # But it will leave everything inside <script> and <style>
        # It will also remove `\r`, `\n`, `\t`
        # Plus it combines all the text into a string, instead of a list 
        def get_text(response):
            with_duplicated_space = ' '.join(response.xpath(
                './/text()[not(ancestor::script|ancestor::style|ancestor::noscript)]').extract()).strip().replace("\r", "").replace("\n", "").replace("\t", "")
            without_duplicated_space = re.sub(
                ' +', ' ', with_duplicated_space).strip()
            return without_duplicated_space

        yield {
            'title': response.css('title::text').get(),
            'text': get_text(response)
        } 
```

Enter fullscreen mode Exit fullscreen mode

好吧，好吧，有很多事要做。如果我的解释令人困惑，请让我知道。我总是乐于接受反馈。在推特上给我发推文

让步骤，删除您的`result.json`并重新运行`scrapy crawl test -o result.json`。并等待爬行过程完成

# 用 Wordcloud 可视化

好了，现在打开朱庇特笔记本。您可以安装

Python 3:

```
python3 -m pip install --upgrade pip
python3 -m pip install jupyter 
```

Enter fullscreen mode Exit fullscreen mode

Python 2:

```
python -m pip install --upgrade pip
python -m pip install jupyter 
```

Enter fullscreen mode Exit fullscreen mode

取决于您的 python 版本

你可以在这里阅读更多安装说明[https://jupyter.org/install.html](https://jupyter.org/install.html)

创建一个文件`File > New Notebook > Python 3`

```
jupyter notebook 
```

Enter fullscreen mode Exit fullscreen mode

```
import json

text = ''
with open("result.json", "r") as read_file:
    data = json.load(read_file)
    for i in data:
        text = text + i['text']

from wordcloud import WordCloud, STOPWORDS, ImageColorGenerator
import matplotlib.pyplot as plt

wordcloud = WordCloud(background_color="white").generate(text)

stopwords = set(STOPWORDS)
# Most of my content is in Bahasa Indonesia. So I am adding more stopwords in Bahasa Indonesia stopwords.update(
    [
        "di", "yang", "dengan", "dan", "pada", "juga",
        "itu", "dalam", "kepada" "untuk", "tersebut", "tidak", "ini",
        "dari", "sebagai", "ada", "ke", "adalah", "akan", "saat", "kembali"
        "oleh", "karena", "tak", "ia", "setelah", "sudah", "atau", "untuk", "bahwa",
        "saya", "next", "play", "menjadi", "dia", "telah", "seperti", "oleh",
        "duration", "minutes", "kami",  "kita", "WIB", "ago", "now", "orang",
        "mereka", "Namun", "Video", "seconds", "views",
    ]
)

wordcloud = WordCloud(background_color="white", stopwords=stopwords, scale=2).generate(text) 
```

Enter fullscreen mode Exit fullscreen mode

好的，现在让我们试着想象一下

```
plt.figure()
plt.imshow(wordcloud, interpolation="bilinear")
plt.axis("off")
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/9ddb5361ac86a343d34a6cc13631af4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eOhcH16K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m8jvp0x5inuezzh1err5.png)

你可以看到`Sukijan`与`Indonesia`、`Sukirman`高度并列，还有`Eka Gustiwana`。

但是我们如何将它保存到文件中呢？简单地说

```
wordcloud.to_file("sukijan.png") 
```

Enter fullscreen mode Exit fullscreen mode

现在应该有一个名为`sukijan.png`的文件包含了单词 cloud

我很想听到你的一些反馈:)。在推特上给我发推文

本文原载于[https://muhajir . dev/writing/scraping-and-visualizing-word cloud/](https://muhajir.dev/writing/scraping-and-visualizing-wordcloud/)