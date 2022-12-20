# 让我们来谈谈网络驱动

> 原文：<https://dev.to/meudeusdoceunaoencontronome/lets-talk-about-webdrivers-2f4l>

# 互联网

你的计算机和服务器(谷歌服务器，亚马逊服务器)之间的连接，这些都是基于 HTTP(或 HTTPS)请求和“代码”文件，如 Html，CSS 和 Javascript。作为客户端-服务器连接，你的电脑安装了一个客户端软件，可以是 Google Chrome、Firefox 或任何其他软件。这个软件管理客户端和服务器之间的通信。

所以我们可以这样组织:你(Web 浏览器)使用 HTTP(S)作为通信协议请求服务器(在线服务)。当文件由服务器传送时，浏览器可以设计网站并加载其功能。

好的，这是一个基本的回顾。

# [t1 a 网络驱动程序](#a-webdriver)

我们现在可以进一步研究客户的解释。

webdriver 伪装成 web 客户端，像普通用户一样向服务器发出请求。这样，软件可以访问每个页面的 Html，在需要的时候捕获信息。

我可以给你一些使用示例，我来自巴西，这里有一些初创公司和大公司每天都在使用这种系统，金融行业使用这种系统来捕获有关客户资金状况的信息，其他公司可以使用这种系统在政府网站上检查公司的资金状况。

# 它是如何工作的

在 HTTP 或 HTTPS 请求中，我们有 CRUD 操作(创建、读取、更新和删除)。在 HTTP 中，它被称为 GET(读取)、POST(创建)、PUT(更新)、DELETE(删除)。这是首先要知道的。

HTTP 请求带有一个主体和一个头，当它是一个 POST 或 PUT 请求时，它应该有一个主体内容。当请求是 GET 或 DELETE 时，它通常没有正文内容，只有标题和 URL。

我们知道我们需要伪装成一个浏览器，这样我们就可以接收我们想要的信息。但是我们怎么知道什么时候该做什么样的请求呢？通常你必须使用“嗅探器”来跟踪你的网络客户端发送的每个请求，然后做完全相同的请求。

我喜欢用“提琴手”([https://www.telerik.com/fiddler](https://www.telerik.com/fiddler))它使用起来非常简单，你可以只针对网络客户端，跟踪任何你想要的导航请求。当然，您必须以 web 客户端为目标并访问 web 站点，这正是您希望 web 驱动程序处理的路径。

最难的部分是理解什么请求被完成，什么时候和为什么，当然还有每个请求的变量在哪里。

# 建筑

一切都是状态机，你的网络驱动也不例外。我将展示一个架构的模型，它对你的使用可能是好的也可能是不好的，但是我已经在我的网络驱动中多次使用这个架构了。

这是一个非常简单的架构，不用特定的语言来描述。

目标:

-绘制导航的所有页面。
-每个请求和每个页面的参数。
-cookie(参数和导航)。
-识别每一页中感兴趣的信息(正则表达式)。

所以在你的代码中你需要...

```
Enum (example):

    -Page 1.
    -Page 2.
    -Page 3.
    -Page 4.

Extract (example)

    -Url
    -Dictionary<string, string> query
    -List<string> regexs
    -Dictionary<string, string> header
    -object body

Dictionary <Enum, Extract> (example):

    -Page 1, Extract.
    -Page 2, Extract.
    -Page 3, Extract.
    -Page 4, Extract. 
```

和主要功能(举例):

```
-Loop (navigation.situation != End):

    -Execute the functions NavigateTo(currentPage).
    -Get the response content (Html, JS and CSS).
    -Execute the regex of the page.
    -Get the parameters (including cookies, and Html         
    variables) for the next page navigation.

-End Loop 
```

最后是提取模块，可以用标记语言、数据库、表格和任何你想做的东西来完成。

这是对 webdrivers 及其架构的介绍，当然这是一个复杂的主题，这还不到它的一半。通常做的是制作框架来处理 Html 阅读(例如正则表达式)，甚至是导航的自动化。使用 XML 标签来描述正则表达式、url、查询 URL 和其他属性，它可以被上传到一个通用的 webdriver，它将使用上传的 XML 来导航您需要的任何网站。