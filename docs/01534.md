# 使用木偶师抓取开发工具:设备和搜索

> 原文：<https://dev.to/damcosset/scraping-dev-to-with-puppeteer-devices-and-search-3651>

# 简介

在上一篇文章中，我们开始在一个小命令行应用程序中使用 puppeteer。我们截图，模拟点击，生成 pdf。在第二篇文章中，我们将继续构建我们开始的应用程序。这一次，我们将添加以下功能:

*   给定一个设备，我们将截取显示在该设备上的 dev.to 主页的屏幕截图。
*   给定一个搜索查询，我们将检索 dev.to 上显示的文章标题、作者、反应和评论。

## 截图设备

首先，让我们创建一个名为`screenshots-devices`的文件夹，我们将在其中存储截图。

所以，这个概念是这样的:木偶师提供了一个设备列表，我们可以在上面查看我们的应用程序是什么样子的。

首先，让我们在 switch 语句中添加 case 来处理我们的新功能。该函数将被称为`getScreenshotDevice`。我们将在命令行中使用的参数是`getScreenDevice`。

```
switch (process.argv[2]) {
    case 'getScreen':
        getScreenshot(process.argv[3])
        break
    case 'getPDF':
        getPDF(process.argv[3])
        break
    case 'getScreenDevice':
        getScreenshotDevice(process.argv[3])
        break
    default:
        console.log('Wrong argument!')
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在需要创建`getScreenshotDevice`函数。

```
const getScreenshotDevice = async device => {
    try {
        const d = puppeteer.devices[device]
        const browser = await puppeteer.launch()
        const page = await browser.newPage()
        await page.emulate(d)
        await page.goto('https://dev.to')
        await page.screenshot({
            path: `screenshots-devices/${device}.png`,
            fullPage: true
        })
        await browser.close()
    } catch (e) {
        console.log(e)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

该函数接受一个参数，即我们想要显示 dev.to 主页的设备。我们可以通过*木偶师找到我们可以使用的设备。一些例子:*

*   iPhone 6
*   iPhone X
*   苹果平板电脑
*   像素 2 风景

所有支持的设备都可以在[这里](https://github.com/GoogleChrome/puppeteer/blob/master/lib/DeviceDescriptors.js)找到。

从木偶师那里检索到正确的设备信息后，我们使用`page.emulate(device)`来确保木偶师使用正确的设备。在那之后，这与我们使用的其他截图功能非常相似。我们只是将结果保存在不同的文件夹中。

*注意:*如你所见，一些设备的标签中有一个空格。为了确保整个标签将被视为命令行中的一个参数，我们需要使用引号。当然，如果标签是一个单词，引号可以省略。

`node index.js getScreenDevice 'iPhone X'`
`node index.js getScreenDevice 'iPhone 6'`
`node index.js getScreenDevice iPad`

通过运行这些命令，您将在指定的设备上获得 dev.to 主页的屏幕截图。这是一个很棒的小工具，可以用来查看您的应用程序在特定设备上是如何显示的。

## 搜索查询

这一次，我们将为我们的工具提供一个字符串，并将其用作 dev.to 中的搜索查询。

我们要做的是:

*   从用户处获取字符串查询
*   前往*dev.to/search?q=myStringQuery*
*   阅读显示的元素

所以，首先，我们需要添加一个特例来处理正确的参数。姑且称之为`query`，调用函数`getQueryResults`。

```
switch (process.argv[2]) {
    case 'getScreen':
        getScreenshot(process.argv[3])
        break
    case 'getPDF':
        getPDF(process.argv[3])
        break
    case 'getScreenDevice':
        getScreenshotDevice(process.argv[3])
        break
    case 'query':
        getQueryResults(process.argv.slice(3))
        break
    default:
        console.log('Wrong argument!')
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们给出了`process.argv.slice(3)`作为函数参数。就像以前的设备一样，我希望能够在我的搜索查询中使用几个词。有两种方法可以做到这一点:

*   把单词放在引号内，就像我们之前做的那样。
*   使用 slice 将所有单词放入一个数组中。

这一次，我们将把命令行中给出的所有单词放在一个数组中的`query`命令之后。

所以，让我们创建我们的`getQueryResults`函数。

```
const getQueryResults = async query => {
    console.log(`Query results:\n -------------------`)
    try {
        const browser = await puppeteer.launch()
        const page = await browser.newPage()
        await page.goto(`https://dev.to/search?q=${query.join('%20')}`)
        await page.waitForSelector('.single-article')

        const articles = await page.$$('.single-article')

        for (let i = 0; i < articles.length; i++) {
            let title = await articles[i].$eval('h3', t => t.textContent)
            let author = await articles[i].$eval(
                'h4',
                a => a.textContent.split('・')[0]
            )
            let tag = ''
            let numberOfReactions = 0
            let numberOfComments = 0
            if (title.startsWith('#')) {
                tag = await articles[i].$eval('span.tag-identifier', s => s.textContent)
            }
            title = title.substring(tag.length)

            let likes = await articles[i].$('.reactions-count')
            let comments = await articles[i].$('.comments-count')
            if (likes) {
                numberOfReactions = await likes.$eval(
                    '.engagement-count-number',
                    span => span.innerHTML
                )
            }

            if (comments) {
                numberOfComments = await comments.$eval(
                    '.engagement-count-number',
                    span => span.innerHTML
                )
            }

            console.log(
                `${i +
                    1}) ${title} by ${author} has ${numberOfReactions} reactions and ${numberOfComments} comments.`
            )
        }

        await browser.close()
    } catch (e) {
        console.log(e)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要做到这一点，我们需要研究一下 HTML 的结构。但是首先，我们用 *%20* 字符连接数组中的每个元素，以便在 url 中使用我们的搜索。然后我们转到适当的开发搜索页面(/search？q=...).

到目前为止，一切顺利。现在，每个结果都包含在一个具有*单篇文章*类的元素中。我们等待他们加载( *waitForSelector* )。然后我们使用*页面检索文章。\$\$* 函数，它将一个选择器作为其参数。我们现在在 articles 变量中有了所有的结果。

这是我们必须研究 HTML 标记的部分，以便知道在哪里寻找我们需要的信息。

*   标题存在于 h3 标签中。但是，我不想要像#showdev 或# discuss 这样的标签。因此，我们将通过使用 tag-indicator 类检索 span 内的值来移除它。
*   作者生活在 h4 标签中。在这个标签中，还有文章发表的日期。一个简单的 String.split 方法将得到我们需要的作者姓名。
*   反应和评论遵循相同的逻辑。它们分别存在于一个包含 reactions-count 类或 comments-count 类的 div 中。通过使用 *\$* 方法，我们将获得元素，如果不存在，则为 null。如果有反馈或评论，我们将通过使用 engagement-count-number 类查看 span 的内容来检索它们的数量。

最后，我们把信息打印到控制台上。

所以，如果我运行`node index.js query puppeteer`，我会得到以下结果:

```
Query results:
 -------------------
1) Generate a PDF from HTML with puppeteer by Damien Cosset has 191 reactions and 11 comments.
2) Front End Development automation with Puppeteer. Part 1 by Jaime Rios has 102 reactions and 0 comments.
3) An introduction to Puppeteer and Headless Chrome by Mohamed Oun has 33 reactions and 2 comments.
4) Generating PDF from HTML with Node.js and Puppeteer by Mate Boer  has 95 reactions and 6 comments.
5) Front End Development Automation with Puppeteer. Part 3 by Jaime Rios has 41 reactions and 4 comments.
6) Mocha and puppeteer on circleCI by Md. Abu Taher 👨‍💻 has 39 reactions and 0 comments.
7) Build a Car Price Scraper-Optimizer Using Puppeteer by Lex Martinez has 23 reactions and 3 comments.
8) Front End Development Automation with Puppeteer. Part 2 by Jaime Rios has 34 reactions and 0 comments.

... more results 
```

Enter fullscreen mode Exit fullscreen mode

第二篇就到这里。你可以在 [Github](https://github.com/Tagada85/cli_scraping) 上找到代码。

快乐编码< 3