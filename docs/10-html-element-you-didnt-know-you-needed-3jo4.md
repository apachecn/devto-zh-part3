# 你不知道你需要的 10 个 HTML 元素

> 原文：<https://dev.to/emmabostian/10-html-element-you-didnt-know-you-needed-3jo4>

我已经听了无数次“HTML 很容易”这样的话。虽然我同意 HTML 可能比其他编程语言更容易学习，但你不应该认为这是理所当然的。

HTML 是一种功能强大的标记语言，可以用来为我们的 web 应用程序提供结构和强大的可访问性优势，但只有在使用得当的情况下。

因此，今天我们将发现十个你可能不知道的 HTML 元素，希望你能创建更易访问的、结构合理的 web 应用程序。

如果你想学习更多关于 HTML 的知识，你可以访问 [W3Schools](https://www.w3schools.com/tags/) 来获得更多的 HTML 元素。

# 音频

标签定义了一种声音，比如音乐或其他音频流。目前支持三种文件格式:MP3、WAV 和 OGG。

[https://codepen.io/emmawedekind/embed/KEeGvP?height=600&default-tab=result&embed-version=2](https://codepen.io/emmawedekind/embed/KEeGvP?height=600&default-tab=result&embed-version=2)

[![audio](img/4efa243dff06f6466d660780d64b1414.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nGjFmT50--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AGhqkT6tNG8M5bCYDyZUKEg.png)

# 批量报价

标签指定了从其他来源引用的部分。

[https://codepen.io/emmawedekind/embed/eXKPeZ?height=600&default-tab=result&embed-version=2](https://codepen.io/emmawedekind/embed/eXKPeZ?height=600&default-tab=result&embed-version=2)

[![blockquote](img/9eb4b30c121c3a2b475dffc77c8986c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H3ODM9Kv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AKzvqrVvveN8IrdylL8tAQg.png)

# 输出

`<output>`标签表示计算的结果。您可以使用加号和等号来指示第一个和第二个输入值应该“输出”到输出标记；您可以用包含要组合的两个元素的 id 的`for`属性来表示这一点。

[https://codepen.io/emmawedekind/embed/zbamRN?height=600&default-tab=result&embed-version=2](https://codepen.io/emmawedekind/embed/zbamRN?height=600&default-tab=result&embed-version=2)

[![output](img/63aabc95f03a11571046969d507b0139.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZkJb36in--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AA3MyPtKoCy7KiQfeMCTr_w.png)

# 图片

`<picture>`标签允许您指定图像来源。您可以设计多个图像来填充浏览器视口，而不是根据视口宽度放大和缩小图像。

图片标签包含两个不同的标签:一个或多个`<source>`元素和一个`<image>`元素。

源元素具有以下属性:

*   `srcset`(必选):定义要显示的图像的 URL
*   `media`:接受您可能在 CSS 中定义的任何有效的媒体查询
*   `sizes`:定义单个宽度值、单个带宽度值的媒体查询或带宽度值的媒体查询的逗号分隔列表
*   `type`:定义 MIME 类型。

浏览器使用属性值来加载最合适的图像；它使用匹配命中的第一个`<source>`元素，并忽略后续的源元素。

如果浏览器不支持该元素或者没有一个`<source>`标签匹配，那么`<img>`标签用于提供向后兼容性。

[![picture](img/e1416abb2ca2e4abacd0abd8bd3c05a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xcgO38wD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A7khhupYDoVE0cNaiykBoQg.png)

# 进步

标签代表任务的进度。

`<progress>`标签不能替代`<meter>`标签，因此指示磁盘空间使用或查询结果相关性的组件应该使用`<meter>`标签。

[https://codepen.io/emmawedekind/embed/jJKepE?height=600&default-tab=result&embed-version=2](https://codepen.io/emmawedekind/embed/jJKepE?height=600&default-tab=result&embed-version=2)

[![progress](img/61aef1584cec3a06867db5cd3728c598.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Oe8ETABf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AnQhZIYv1VDdUHvx_mZvy0g.png)

# 计

`<meter>`标签定义了一个定义范围内的标量测量，或一个分数值。您也可以通过名称“仪表”来引用此标签

您可以使用`<meter>`标签来显示磁盘使用统计数据或指示搜索结果的相关性。

标签`<meter>`不应该用来表示任务的进度；这些类型的组件应该由一个`<progress>`元素定义。

[https://codepen.io/emmawedekind/embed/wOXYYL?height=600&default-tab=result&embed-version=2](https://codepen.io/emmawedekind/embed/wOXYYL?height=600&default-tab=result&embed-version=2)

[![meter](img/c39948092f1f0c8022e749590e0826e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sZGZPtqV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Acsl2_IT6gtFQkCMhC09zRg.png)

# 模板

`<template>`标签包含对用户隐藏的内容，但是将被用来重复实例化 HTML 代码。

[![template](img/7c26e1f210df2214636882f6a9d95f49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MCPbgQVf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AkXZen0gUvApkjtqNFKIF-A.png)

使用 JavaScript，您可以用`cloneNode()`方法呈现这些内容。

[![template](img/06fdcbbe8162577150cb0e2f57a343fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zcduNpgA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ah7xi-8gq0SKvxKpW4L1MYg.png)

# 时间

标签定义了一个人类可读的日期或时间。这可以用来以机器可读的方式对日期和时间进行编码，以便用户代理可以将生日提醒或预定事件添加到用户的日历中。此外，这允许搜索引擎产生“更智能”的搜索结果。

[https://codepen.io/emmawedekind/embed/moKzva?height=600&default-tab=result&embed-version=2](https://codepen.io/emmawedekind/embed/moKzva?height=600&default-tab=result&embed-version=2)

[![time](img/72547823ce7cacb8c67a28911b0d58b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xk6Zu7f---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2400/1%2ARMr76nCXN_a9U-7zrPnD3Q.png)

# 视频

`<video>`标签指定一个电影剪辑或视频流。支持的格式包括 MP4、WebM 和 Ogg。

[https://codepen.io/emmawedekind/embed/ywERrJ?height=600&default-tab=result&embed-version=2](https://codepen.io/emmawedekind/embed/ywERrJ?height=600&default-tab=result&embed-version=2)

[![video](img/ef1bcd530e2ecbed059cb660d1202707.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PVmk_JXk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AhJCtx4hIBAzEGY7HRwwUmg.png)

# 字断机遇

如果你有一个很长的文本块，或者一个很长的单词，你可以使用`<wbr>`标签来指定在一个文本体中的什么地方最适合换行。这是确保浏览器在调整大小时不会在奇怪的地方打断文本的一种方法。

[https://codepen.io/emmawedekind/embed/MxXPMb?height=600&default-tab=result&embed-version=2](https://codepen.io/emmawedekind/embed/MxXPMb?height=600&default-tab=result&embed-version=2)

[![wbr](img/df95fcb3f8d3275d6f0b44c5cba3728b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n-GVosrw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AwtEUeRTqYR0DvU-OkZ7tHQ.png)

* * *

我希望这十个 HTML 元素给了你一些新的工具来构建令人敬畏的应用程序！