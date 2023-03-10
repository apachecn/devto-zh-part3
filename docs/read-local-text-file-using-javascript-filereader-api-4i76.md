# 使用 JavaScript FileReader API 读取本地文本文件

> 原文：<https://dev.to/singhdigamber/read-local-text-file-using-javascript-filereader-api-4i76>

[![HTML5 FileReader API](img/b4d0018400882bf6998a0cd7f2336f83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BWvKqCYG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/igk5ns2sywt7ftzebp52.jpg)

在这个小教程中，我将与您分享如何上传一个. txt 文件，并使用 HTML5 文件 API 从客户端读取其内容。

**HTML**
创建一个**index.html**文件并在其中添加文件输入属性。在 index.html 文件中声明文件输入标签以及 **onchange** 事件监听器。当用户选择一个. txt 文件时，这个事件监听器将触发 **showFile()** 函数。

**JavaScript**

在 js 文件中，我们将编写上传文本文件的逻辑。

1 -检查您的浏览器是否支持 HTML5 文件 API

2 -声明 **showFile()** 函数
3 -创建**文件**引用变量以上传文本文件
4 -创建**预览**引用 HTML div 以显示文本
5 -创建引用**reader = new File reader()**API
6-声明**文本文件**变量以应用文本文件验证
7
8 -调用**reader . readas text(file)**方法，并将 **file** 变量传递到 **reader.onload** 方法之外

[https://codepen.io/singhdigamber/embed/jXXJZO?height=600&default-tab=result&embed-version=2](https://codepen.io/singhdigamber/embed/jXXJZO?height=600&default-tab=result&embed-version=2)

这只是一个关于 [HTML5 FileReader API](https://www.positronx.io/understand-html5-filereader-api-to-upload-image-and-text-files/) 读取文本文件的小话题，下面是详细文章[开始使用 HTML5 FileReader API 上传图片和文本文件](https://www.positronx.io/understand-html5-filereader-api-to-upload-image-and-text-files/)