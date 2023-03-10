# 作为初学者如何搭建一个网络爬虫？

> 原文：<https://dev.to/erikafu/how-to-build-a-web-crawler-as-a-beginner-4igf>

使用计算机语言(例如:Python)

对于任何希望使用计算机语言构建 web scraper 的非编码人员来说，与 PHP、Java、C/C++相比，Python 可能是最容易开始的语言。Python 的语法对于任何阅读英语的人来说都相当简单易读。

下面是一个用 Python 写的网络爬虫的简单例子。

* * *

导入队列

```
 initial_page = "http://www.renminribao.com"

        url_queue = Queue.Queue()

        seen = set()

        seen.insert(initial_page)

        url_queue.put(initial_page) 
```

while(True):

```
 if url_queue.size()>0:

        current_url = url_queue.get()

        store(current_url)

        for next_url in extract_urls(current_url):

              if next_url not in seen:

                   seen.put(next_url)

                   url_queue.put(next_url)

   else:

          break 
```

* * *

作为不懂编程的初学者，绝对要求我们花时间和精力学习 Python，然后自己写一个网络爬虫。整个学习过程可能会持续几个月。

使用网页抓取工具(例如:Octoparse)

当一个初学者想在合理的时间内建立一个网络爬虫时，像 Octoparse 这样的可视化网络抓取软件是一个可以考虑的好选择。这是一个免费版本的免费网页抓取工具。与其他网络抓取工具相比，对于任何希望快速抓取网站数据的人来说，Octoparse 都是一个经济高效的解决方案。[五大网页抓取工具对比](https://www.octoparse.com/blog/top-5-web-scraping-tools-comparison)。

如何在 Octoparse 中“搭建一个网络爬虫”？

1.[向导模式，方便刮削](https://www.octoparse.com/tutorial-7/wizard-mode)

向导模式将指导用户一步一步地抓取 Octoparse 中的数据，它提供了三个预建的模板——“列表或表格”、“列表和明细”和“单页”。如果预先构建的模板能够满足我们的需要，我们可以在下载 Octoparse 后点击几下就可以轻松地在 Octoparse 中构建一个“网络爬虫”。

2.[复杂网页抓取的高级模式](https://www.octoparse.com/tutorial-7/advanced-mode)

由于一些网站的结构非常复杂，向导模式无法帮助我们收集所有想要的数据。因此，我们最好使用高级模式，它在抓取数据方面更加强大和灵活。

这是一个如何使用 Octoparse 构建网络爬虫的例子。从亚马逊抓取产品信息。X)