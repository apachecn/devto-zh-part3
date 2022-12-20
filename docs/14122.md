# 用 Python 和 Flask 和 BeautifulSoup 4 实现 Webscraping

> 原文：<https://dev.to/blazephoenix/webscraping-in-python-with-flask-and-beautifulsoup-4-1pkl>

[![](img/530fae1d0354e74b752aa61d5974c747.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kP6u5z7b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vlb3nw2civ5iyd3ats4w.jpg)

## 什么是网页抓取？

网络抓取是一个术语，用于从网站提取 HTML/XML 数据的过程。提取后，它可以被解析成不同的 HTML 文件或保存在本地的文本/电子表格文档中。

## 谁干的？

很多网站从互联网上的其他网站收集数据。例如，在多个平台(亚马逊、Flipkart、易趣等)进行比较后，为您提供同一产品最佳交易的网站。)，以及收集数据集以应用 ML 算法的站点。

## 对我有什么用？

我建议你把你的思考限制在某件事如何对你有益，尤其是当你对它知之甚少甚至一无所知的时候。当你刚刚起步时，成为一名多面手会有所帮助。什么都要学，你永远不知道什么时候会用到它！当你充分意识到你所拥有的选择时，你最终总能在一个领域安顿下来并专攻它。

* * *

## 我们需要什么

*   python 3 . 6 . 8 版
*   虚拟代码

## 安装 Python(如果已经安装，跳过)

*   前往—python.org >下载>窗口
*   滚动到版本 3.6.8 > x86 (32 位)/x86–64(64 位) >可执行文件
*   双击并选中“将 Python 添加到路径”
*   遵循安装说明。
*   检查安装是否正确
*   按 Windows 键+R >键入“cmd”打开命令行。
*   在命令行>中键入，
*   python 版本

如果 Python 安装正确，您应该在终端中看到，`3.6.8`。

## 安装 VSCode(如果已经安装，跳过)

VSCode 是一个免费的代码编辑器，它有很多特性使得编写和调试代码更加容易。

*   转到 code.visualstudio.com > Windows 下载> x86/x64 >安装程序。
*   双击并按照说明操作。

* * *

## 我们开始吧！

*   创建一个新文件夹，命名为“网络刮刀”
*   在该文件夹中，创建一个名为 webscraper.py 的新文件
*   打开 VSCode >文件>打开文件夹>导航到“Webscraper”

现在我们需要导入一些库来帮助我们构建 web scraper。

*   前往终端>新终端

这基本上是命令行，但在编辑器中，所以我们不必有两个窗口，并在它们之间切换。

*   接下来我们调用`pip`

*你可以称之为阿尔弗雷德到蟒蛇的蝙蝠侠。呵呵。*

*   在您的终端中，键入`pip install beautifulsoup4`

这将安装 beautifulsoup 库，它将帮助我们抓取网页。

*   下一个类型`pip install flask`和`pip install requests`

Flask 是一个构建网站的轻量级框架。我们将使用它来解析我们收集的数据，并在一个新的 HTML 文件中将其显示为 HTML。

请求模块允许我们向我们想要抓取的网站发送 http 请求。

在您的文件中，键入以下代码:

```
 from flask import Flask, render_template
    from bs4 import BeautifulSoup
    import requests 
```

Enter fullscreen mode Exit fullscreen mode

第一行从 flask 库中导入 Flask 类和 render_template 方法。第二行导入 BeautifulSoup 类，第三行从我们的 Python 库中导入 requests 模块。

接下来，我们声明一个变量来保存我们请求的结果

```
 source = requests.get(‘https://webscraper.droppages.com/').text 
```

Enter fullscreen mode Exit fullscreen mode

我们发送一个`GET`请求给 https://webscraper.droppages.com 的，将 HTML 转换成纯文本并存储在变量`source`中。

接下来，我们声明一个`soup`变量，并存储我们在将`source`传递给`BS`后得到的值。“lxml”是我们希望呈现的代码具有的标记。

```
 soup = BeautifulSoup(source, 'lxml') 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们有我们的代码工作。您可以通过将 soup 传递给一个打印函数来检查它，就像前面一行之后的这个`print(soup)`并在终端中运行`python webscraper.py`。

* * *

现在，我们正在拉整个网页，而不是特定的元素。要获取具体元素，可以自己尝试这些。

但是在你这样做之前，你应该清楚你到底想要什么。您可以再次运行上一个命令，或者在浏览器中打开网页，并通过右键单击该网页来检查它。这里需要一些 HTML DOM 和 CSS 的知识。你可以去 W3Schools 或 T2 MDN T3 上一堂快速速成课。

```
 <variable> = soup.find('<HTML_element_name>')
    <variable> = soup.find('<HTML_element_name>').select_one('child_element')
    <variable> = soup.find('<HTML_element_name>').find_all('child_element') 
```

Enter fullscreen mode Exit fullscreen mode

您可以在括号中传递常规的 CSS 符号，以便更具体地表达您想要的元素。

现在，我们实际上只是输出 HTML 以及其中的文本。如果我们只想要文本呢？

那很简单。

我们简单地在它的结尾传递`.text`。就像我们对`source`做的那样。这里有一个例子。

```
 head = soup.find(‘main’).select_one(‘article:nth-of-type(4)’).div.text 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们告诉 Python 将第 4 个 article 元素中的 div 文本存储在主元素的 head 变量中。

您可以通过将 head 传递给`print()`并在终端中运行`python webscraper.py`来检查输出。

如果可以的话，试着找一个作者的名字。

* * *

可以得到这样的作者，

```
 author = soup.find(‘main’).select_one(‘p’).text 
```

Enter fullscreen mode Exit fullscreen mode

* * *

注意你是如何得到日期和名字的。那是因为它们有相同的元素。有一种方法可以通过使用类似于`split`和`slice`的 Python 字符串方法单独获得作者姓名。但是我们不会在这里讨论这个。

* * *

接下来，我们将使用 flask 在本地服务器上以我们想要的方式重新呈现接收到的数据。

在您的文件中，键入以下代码，

```
 app = Flask(__name__)
    @app.route('/')
    def index():
       return render_template('index.html,**locals())
    app.run(debug=True) 
```

Enter fullscreen mode Exit fullscreen mode

在 webscraper 主文件夹中创建一个新的模板文件夹，命名为`index.html`

flask 部分解释起来有点复杂，但简单地说，我们创建了一个简单的服务器，它将从 templates 文件夹中取出我们的`index.html`,并在本地服务器上提供它— ` localhost://5000`

现在，我们可以使用 soup 将前面代码中声明的多个变量组合起来，并将文本传递给 HTML，并使用 CSS 按照我们想要的方式对它们进行样式化！

您可以将此代码用于`index.html`文件

```
 <!DOCTYPE html>
    <html lang=”en”>
     <head>
      <meta charset=”UTF-8">
      <meta name=”viewport” content=”width=device-width, initial-scale=1.0">
      <meta http-equiv=”X-UA-Compatible” content=”ie=edge”>
      Webscraper in Python using Flask
     </head>
     <body>
      <!-- Variables from Flask here -->
     </body>
    </html> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以使用到目前为止学到的所有代码来创建自定义变量，并从我们的站点中提取特定的数据。如果我们非常熟悉目标网站的结构，我们可以使用这样的快捷方式。

```
 head = soup.header.h1.text

    second_author = soup.main.select_one(‘article:nth-of-type(2)’).p.text

    first_article = soup.main.article.div 
```

Enter fullscreen mode Exit fullscreen mode

*   在我们创建的`index()`函数中键入这些，就在 return 语句的上面。
*   保存文件
*   去 index.html

现在我们将把这些变量传递到 HTML 中，这样我们就可以在网页上看到这些数据了。

```
 <!DOCTYPE html>
    <html lang=”en”>
     <head>
      <meta charset=”UTF-8">
      <meta name=”viewport” content=”width=device-width, initial-scale=1.0">
      <meta http-equiv=”X-UA-Compatible” content=”ie=edge”>
      Webscraper in Python using Flask
     </head>
     <body>
      <h1>{{ head }}</div>
      <p>{{ second_author }}</p>
      <article>{{ first_article }}</article>
     </body>
    </html> 
```

Enter fullscreen mode Exit fullscreen mode

现在打开终端并运行`python webscraper.py`

### 我们成功了！

如果你想知道它是如何如此简单，那么，它不是。这只是一个简单的页面，没有向 HTML 中添加任何类或 id。但这是一个好的开始。

想知道如何刮多页纸吗？

答案是多重`for`、`while`、`try`、`except`、`if-else`循环！

* * *

你好，这是我的第一篇技术文章。如果您在代码或我学习教程的方式中发现任何错误，请随时纠正我。我很高兴成为这个社区的一部分，因为我与它一起成长，并打算贡献有意义的内容。感谢您的阅读！