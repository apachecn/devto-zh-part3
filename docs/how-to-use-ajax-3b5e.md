# 如何使用$。ajax()

> 原文：<https://dev.to/williamragstad/how-to-use-ajax-3b5e>

## 什么是 Ajax？

> #### 重要！
> 
> 当我在本文中谈到 Ajax 时，我最常提到的是 JQuery 内置的方法。请注意，JQuery 中的 Ajax***是***Ajax 的一种，但 Ajax ***并不是 JQuery 中的 Ajax***。

Ajax 是一种从服务器、API 或其他网页发送和接收数据的方法，已经成为网络通信的标准。

它不是内置在普通 JavaScript 中的，因此你需要使用像 [JQuery](https://jquery.com/) *(例如)*这样的库来使用 Ajax。名字是 **A** 同步 **Ja** vaScript 和 **X** ML 的简称。

> #### Ajax 的定义
> 
> “Asynchronous JavaScript + XML 虽然本身不是一项技术，但这个术语是由 Jesse James Garrett 于 2005 年创造的，描述了一种将许多现有技术结合起来使用的“新”方法。
> T3】...
> 当这些技术结合在 Ajax 模型中时，web 应用程序能够快速、渐进地更新用户界面，而无需重新加载整个浏览器页面。这使得应用程序速度更快，对用户操作的响应更灵敏。”
> 
> *MDN web docs，引自 2019-01-20*[来源文章](https://developer.mozilla.org/en-US/docs/Web/Guide/AJAX)。

## Ajax*常用*如何使用？

好吧，Ajax 可以隐藏在你在网上看到的任何东西中，从你在周五下午滚动时异步加载你的 Twitter feed，到从购物网站的管理面板添加新产品。

Ajax 主要用于在运行时从客户端获取数据。

## 如何开始使用 Ajax？

假设你已经有了一个 [HTML](https://sv.wikipedia.org/wiki/HTML) 、 [PHP](http://www.php.net/) 或类似的文件，以及一个工作的互联网连接，你可以直接从他们的网站下载 JQuery 并链接到你的服务器上作为一个文件，或者使用一个 [CDN](https://en.wikipedia.org/wiki/Content_delivery_network) 获得它。

在本文中，我将向您展示如何使用 JQuery 的公共 CDN 来获得它。

#### 1。获取最新版本的 JQuery

访问[这个页面](https://code.jquery.com/)，这里有一个 JQuery 不同版本的列表。我建议你挑一个小包装的。复制所示的 HTML 代码，它应该是这样的:

```
<script src="https://code.jquery.com/jquery-3.3.1.min.js"
  integrity="sha256-FgpCb/KJQlLNfOu91ta32o/NMZxltwRo8QtmkMRdAu8="
  crossorigin="anonymous"></script> 
```

Enter fullscreen mode Exit fullscreen mode

#### 2。打开你的 HTML/PHP/ *等*文件

将脚本标签粘贴到页面的顶部(在 head 标签中)或底部(在 body 标签下)。**我建议你把它放在底部**以防在加载 JQuery 库时出现问题，你们中的一些人可能会有其他偏好。

```
<!doctype html>
<html>
    <head>
        My Site
        <!-- Here -->
    </head>
    <body>
        Hello World
    </body>
    <!-- Or Here -->
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 3。搞定了。

## HTTP 方法

当通过 web 发送请求和数据时，我们可以使用两种主要方法之一:

1.  邮政
2.  得到

(在这里阅读更多关于 [HTTP 请求方法的信息](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods)或者在这里阅读[(W3Schools)](https://www.w3schools.com/tags/ref_httpmethods.asp))。

[![Difference between post and get](img/42ae30de9aa3d5719bad666d5326604d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZNMau5es--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ytxdmm71yvt3zokkrpn4.jpg)

## 岗位

Post 用于发送“不可见”的数据，在大多数情况下，post 是一种好方法，因为与 get 相比，post 可以发送更多的数据。

### 安全

如果使用该页面的客户端通过 [WPA 或 WPA2](https://sv.wikipedia.org/wiki/WPA) 连接到安全 WI-FI，post 方法是最安全的方法。即使用户使用的是未受保护的网络，get 也可能更好...

### 申请

Post 通常用于传输或接收少量到大量的数据。如前所述，它甚至在与 web 服务器、数据库或 API 通信时使用。

例如，当你有一个购物篮，里面有一些你想买的产品，当你点击“继续”并被重定向到另一个页面时，你的产品会跟随你。(这也可以由服务器使用会话来处理，但这不在本文中讨论。)

## 得到

大多数人听到“Get”时想到的是 URL 查询。get 方法通常是用户**手动**与网页或 API 通信的方式，指定搜索词、日期范围或定义查询的其他信息。

Wikipedia 将 HTTP get 请求方法描述为仅从服务器检索信息。其中一些数据可以通过网页 URL 或“查询字符串”来配置。*(见下图)*

[![URL Query example](img/0840e6b88ac56ff908939c92902b4cd5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WTASYQss--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www-growyourgk-com.cdn.ampproject.org/i/s/www.growyourgk.com/wp-content/uploads/2017/08/querystring-in-javascript.jpg)

得到的是更*开放*给用户。这有利也有弊。

### 优点

如果方法正确，get 可以是一种与网页对话并定制他/她的体验的方式。比如说；您访问一个带有一些控件的站点，更改这些控件并应用更改会导致页面重新加载，而且 URL 中还会有一些附加代码。这被称为 URL 查询。在这里，您可以看到您所更改的那些控件的值，并且现在可以直接从 URL 更改它们。只需输入一个新值，然后按回车键。这通常由 API 服务使用。

### 弊

将控制权交给用户总是危险的。最糟糕的情况是使用反射表单通过 get 向不安全的网站提交评论。如果网站容易受到跨站脚本(XSS)的攻击，只是将用户输入的所有内容打印到源页面...Get 可能被利用为 ex 的一个走法；铬 XSS 检测，内置保护系统等。

当然还有更多的利弊要考虑。但这只是让你快速地看一眼。

## 比较

Get *only* 允许你发送键值对，不像 post 可以发送整个对象、文件、表格等等。了解更多关于[的能力，获得&职位](https://en.m.wikipedia.org/wiki/POST_(HTTP))。

> ## 重要！
> 
> 还记得我们粘贴使我们能够使用 JQuery 的脚本标签吗？嗯，代码总是以级联顺序执行，依赖其他代码的代码必须在它所依赖的代码之后执行。我想说的是总是把你自己的依赖 JavaScript 的脚本标签放在 JQuery 标签下面。否则，这可能会停止执行您的网站，不能按预期运行 JavaScript。

* * *

# 用法

行...让我们最终进入正题。

通过键入`$`可以访问 Ajax 方法。这就是 [jQuery 对象](https://learn.jquery.com/using-jquery-core/jquery-object/)。接下来是一个`.`，您将看到 JQuery 提供的所有功能的列表。

## 功能

### $。创建交互式、快速动态网页应用的网页开发技术

jQuery 的核心`$.ajax()`方法是一种创建 Ajax 请求的强大方法。它需要一个包含 Ajax 发送请求所需的所有配置的对象。

#### 参数

| 参数 | 描述 |
| --- | --- |
| 类型/方法 | 将使用哪种类型的 HTTP 方法？ |
| 全球资源定位器(Uniform Resource Locator) | 我们将把这个请求发送到哪里？ |
| 数据 | 我们要向目的地发送什么数据？ |

#### 事件

| 事件 | 描述 |
| --- | --- |
| 成功 | Ajax 返回任何数据时调用的函数。 |

这方面的一个例子可能如下:

```
$.ajax({
    type: "post",
    url: "www.example.com/subfolder/page.php",
    data: {
        myLuckyNumber: 13
    },
    success: function(response) {
        console.log(response);
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Lucky number was registered! 
```

Enter fullscreen mode Exit fullscreen mode

### $。邮政

使用 HTTP POST 请求从服务器加载或发送数据。它需要一个包含 Ajax 发送请求所需的所有配置的对象。

#### 参数

| 参数 | 描述 |
| --- | --- |
| 全球资源定位器(Uniform Resource Locator) | 我们将把这个请求发送到哪里？ |
| 数据 | 我们要向目的地发送什么数据？ |

#### 事件

| 事件 | 描述 |
| --- | --- |
| 成功 | Ajax 返回任何数据时调用的函数。 |

这方面的一个例子可能如下:

```
$.post({
    url: "www.api.mydomain.com/register.php",
    data: {
        users: {
            0: {                // User 1, aka data.users[0]
                name: "Joe",    //data.users[0].name
                age: 54,        //data.users[0].age
                work: "hacker"  //data.users[0].work
            },
            1: {
                name: "Billy",
                age: 28,
                work: "road worker"
            },
            2: {
                name: "Nathan",
                age: 15,
                work: "unemployed"
            }
        }
    },
    success: function(response) {
        console.log(response);
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
3 new user(s) where registered! 
```

Enter fullscreen mode Exit fullscreen mode

### $。得到

使用 HTTP GET 请求从服务器加载或发送数据。它需要一个包含 Ajax 发送请求所需的所有配置的对象。

#### 参数

| 参数 | 描述 |
| --- | --- |
| 全球资源定位器(Uniform Resource Locator) | 我们将把这个请求发送到哪里？ |
| 数据 | 我们要向目的地发送什么数据？ |

#### 事件

| 事件 | 描述 |
| --- | --- |
| 成功 | Ajax 返回任何数据时调用的函数。 |

这方面的一个例子可能如下:

```
$.get({
    url: "https://www.google.se",
    success: function(response) {
        console.log(response);
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="sv">
    <head>
    <meta charset="UTF-8">
    <meta content="origin" name="referrer">
    <meta contentimg/branding/googleg/1x/googleg_standard_color_128dp.png" itemprop="image">
    <meta content="origin" name="referrer">
    Google
    <script>
    ...
    </script>
    <style>
    ...
    </style>
... 
```

Enter fullscreen mode Exit fullscreen mode

## 用 Ajax 执行 JavaScript 文件！

加载并执行一个 JavaScript 文件。

```
$.ajax({
  method: "GET",
  url: "test.js",
  dataType: "script"
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 用 Ajax 解析！

因为 Ajax 向 web 服务器发送 HTTP 请求，所以它也接收 HTTP/HTML/JSON 格式的响应。记住这一点，我们可以假设我们的 Ajax 是我们的浏览器，并向 web 服务器发送请求，例如我们想要一个普通的 html 页面。

> ### &[CORB](https://www.chromestatus.com/feature/5629709824032768)政策
> 
> **跨源资源共享***【CORS】*是附加的 HTTP 头，它告诉浏览器让运行在一个*源*或域的 web 应用程序有权访问来自不同*源*或域的服务器的选定资源。这意味着没有相同*来源*或域名**的网页可以共享资源** *(见下图)*。
> 只要服务器被配置为允许来自您或任何其他 web 应用程序来源的请求， [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 和 **Ajax** 就可以工作。
> 
> 可悲的是，一些网站受到一种名为**跨源读取阻止***【CORB】*的东西的保护。这是一种算法，可以在 web 浏览器中的“跨来源”资源负载到达网页之前识别并阻止它们。这意味着没有相同*来源*或域名**的网页不能共享资源**。
> 
> [![CORS principle](img/be2f703a765573b17d0b7e612689c327.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EpEnq3ZK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mdn.mozillademos.org/files/14295/CORS_principle.png)

如果你看下面的代码，一个新的 Ajax 请求被创建到一个使用 CORS 的站点。这意味着我们可以通过 Get 方法请求 HTML 代码。

```
$.ajax({
  url: "https://www.google.com",    // This is just an example domain
  method: "GET",
  sucess: function(sourceCode) {
    console.log(sourceCode);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="sv">
    <head>
    <meta charset="UTF-8">
    <meta content="origin" name="referrer">
    <meta contentimg/branding/googleg/1x/googleg_standard_color_128dp.png" itemprop="image">
    <meta content="origin" name="referrer">
    Google
    <script>
    ...
    </script>
    <style>
    ...
    </style>
... 
```

Enter fullscreen mode Exit fullscreen mode

## Ajax 进步！

在 JQuery 中，没有直接的方法来获取一个`$.ajax()`请求的进度，但是该方法支持一个定制的 **xhr** 对象。

在下面的例子中，我们创建了自己的 xhr 对象来执行 HTTP 请求，并为它添加了一个事件监听器。这使得我们能够以请求的百分比来获取当前状态。

> #### xhr
> 
> `$.ajax()`函数返回它创建的 XMLHttpRequest 对象。通常 jQuery 在内部处理这个对象的创建，但是可以使用 xhr 选项指定一个定制函数来创建一个对象。
> 
> *JQuery AJAX 文档，引自* [文档](https://api.jquery.com/jQuery.ajax/)2019-01-20。

```
$.ajax({
    url: "https://example.url/very/large/image.jpeg",
    method: "get",

    xhr: function() {
            var xhr = new window.XMLHttpRequest();
            xhr.addEventListener("progress", function(evt) {
                if (evt.lengthComputable) {
                    var percentComplete = evt.loaded / evt.total;
                    console.log(percentComplete);
                }
            }, false);
            return xhr;
        }
}) 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
0.2384341119115186
0.7248216529871057
1 
```

Enter fullscreen mode Exit fullscreen mode

也可以通过将`xhr.addEventListener(...)`替换为`xhr.upload.addEventListener(...)`来获取联系服务器的进度，而不是捕获响应

* * *

## 闭合

感谢阅读这篇文章！我希望它能帮助你成为 JavaScript 和 JQuery 大师！与你的朋友和家人分享，他们可能会对这些信息感兴趣或从中受益。

如果你有任何问题，我很乐意在下面的讨论中回答。

* * *

### 学分

*   克里斯托弗·基弗拍摄的封面图片
*   [由我发布和获取的区别](https://thepracticaldev.s3.amazonaws.com/i/ytxdmm71yvt3zokkrpn4.jpg)
*   Hari Prasad 的 [Url 查询示例](https://www-growyourgk-com.cdn.ampproject.org/i/s/www.growyourgk.com/wp-content/uploads/2017/08/querystring-in-javascript.jpg)
*   [CORS 法则](https://mdn.mozillademos.org/files/14295/CORS_principle.png)由 MDN 网络文档

### 帮助使这篇文章更好的人

*   [迈克尔·戈德斯平纳](https://dev.to/emgodev)反馈