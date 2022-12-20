# 在 Express/React/React 路由器应用程序中呈现 HTML 文件

> 原文：<https://dev.to/damcosset/rendering-html-files-in-an-express-react-react-router-application-nbk>

大家好。从来没有张贴在这里的代码问题，但我真的卡住了:D

我有一个代码编辑器应用程序。我能打开文件，编辑并保存它们。有一次，我想在一个新的选项卡中预览 html 文件，以便看到我所做的更改。现在，这些是全页 html，就像这样:

```
<html>
    <head>
        <link ... />
        <script src="..." />
    </head>
    <body>...</body> </html> 
```

我可以获取 HTML 内容，但是我找不到在新标签页中正确加载 HTML 的方法。

我尝试使用 window.open(filePath)。问题是我得到了一个【the HTML 将在 React 应用程序标记中呈现，这不是我想要的。

我试图使用一些 iframe，但是失败得很惨。

我认为，我可能错了，我需要绕过 react-router 指令来使用 Express 中的 response.renderFile。我该怎么做呢？

因此，使用文件路径发出 post 请求，打开一个新的选项卡，并从 express 中呈现 HTML 内容。对吗？

有什么建议吗？:)

**编辑:**

算是解决了我的问题。我正在使用下面的代码:

```
childUrl = window.open('/preview', '_blank')
childUrl.document.open('text/html', 'replace')
childUrl.document.write(htmlContent) 
```

我对此仍有一点异议。外部文件(脚本和链接)的路径需要从顶层开始，不能是相对的。所以，我想问题解决了？