# 使用 Python 写入文件函数

> 原文：<https://dev.to/kite/using-the-python-write-to-file-function-4m3>

[![Python Write to File](img/9cd225f6da76cc0944093770145121d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_w2vinVL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/slrqscbk60hj0fodhb3i.jpg)

在有这么多其他选择的情况下，开发人员和客户如何从文件格式的数据中获益？为什么不使用带有 AJAX 请求和表格的简单 HTML 页面来显示数据呢？在这篇博文中，让我向您展示为什么使用 Python 来构造文件是创建和填充数据的有效方式。我们将研究一些用数据填充文件的 API，并学习如何使用 Requests HTTP 库获取数据。在这篇文章中，我们将使用 Python 3 及其工具包。我们开始吧！

您可以在[Kite’github 库](https://github.com/kiteco/kite-python-blog-post-code/tree/master/python-write-to-file)中轻松访问本文中的代码。

## 设定目标

我们的第一步是为我们的文件设定一些目标。

*   首先，我们需要一些灵感:为此，我们将使用 Python 中的 Requests 模块向一个著名的 quotes API 发出请求。
*   第二，我们希望确保我们在股票市场的投资看起来稳健。我们将在投资者交易所开发者 API 周围使用 IEXFinance Python 包装器。
*   第三，我们还想确保我们的加密货币也是绿色的。我们将使用来自比特币基地 API 的请求模块获取数据，以检查我们的数字资产。
*   最后，我们将获取所有这些获取的数据，并写入与每次运行脚本相对应的文件中，并有一个累积文件。我们所有的文件都将是 CSV 格式。

我假设我们将利用电子表格软件的功能来绘制存储在文件中的数据。让我们深入了解请求 HTTP 库是如何工作的，并获取我们的报价！

在风筝博客上阅读更多[！](https://kite.com/blog/python/python-write-to-file)