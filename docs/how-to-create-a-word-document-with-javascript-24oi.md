# 如何用 JavaScript 创建 Word 文档

> 原文：<https://dev.to/iainfreestone/how-to-create-a-word-document-with-javascript-24oi>

### 简介

Word 文档无处不在，用于无数的任务，因此您可能需要支持从您的 web 应用程序或网页中生成这些文档，这可能包括从您的个人网站下载一份简历，然后根据您的 web 应用程序中的数据生成一份报告，您的客户可以打印或通过电子邮件发送该报告。

### 目标

仅使用客户端普通/普通 JavaScript 创建和保存 Word 文档。

### 先决条件

为了完成这个例子，你应该有一些 HTML 和 JavaScript 的基础知识。

### 步骤 1 -设置项目

为了专注于手头的工作，并尽快启动和运行，我们将使用 CodeSandbox 和他们的 vanilla starter 项目。

打开下面的链接 [vanilla JavaScript project](https://codesandbox.io/s/vanilla) 可以创建一个新的 CodeSandbox 项目。

### 步骤 2 -安装创建 Word 文档所需的依赖项

有了项目设置，我们现在可以安装成功创建和保存 Word 文档所需的两个模块/依赖项。第一个包( [docx](https://github.com/dolanmiu/docx) )允许你构建你的 Word 文档，第二个包([文件保存器](https://github.com/eligrey/FileSaver.js/))提供了在本地保存文档的能力。

使用“Add Dependency”按钮添加这两个包。

[![](img/2d60d9f29244ded3bcb8ce94fbf9a279.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FGfxx-o6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xw1oqyibfq9339sfal6n.png)

[![](img/3c4c261879e38af51bda74831eeb0551.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9sAYmA2K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n74maah2c5ubscjqolkr.png)

### 第三步-添加按钮创建我们的 Word 文档

在我们开始编写生成 Word 文档所需的代码之前，让我们给 index.html 文件添加一个按钮。然后，我们可以使用这个按钮来启动生成 Word 文档所需的操作。

```
<!DOCTYPE html>
<html>
  <head>
    Parcel Sandbox
    <meta charset="UTF-8" />
  </head>

  <body>
    <div id="app"></div>
    <button id="generate">Generate Word Document</button>
    <script src="src/index.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 4 -创建一个空白的 Word 文档

从现在开始，我们将只在 index.js JavaScript 文件中工作，让我们从删除当前所有不需要的内容开始。

我们创建一个空白 word 文档所需要的只是 docx 模块中的文档和打包功能以及 file-saver 模块中的另存为功能。

```
import { Document, Packer } from "docx"
import { saveAs } from "file-saver" 
```

Enter fullscreen mode Exit fullscreen mode

接下来添加一个事件监听器，监听我们在步骤 3 中创建的按钮上的点击。

```
// Listen for clicks on Generate Word Document button
document.getElementById("generate").addEventListener(
  "click",
  function(event) {
    generateWordDocument(event)
  },
  false
) 
```

Enter fullscreen mode Exit fullscreen mode

一旦事件侦听器被触发，我们将调用一个函数，该函数将从 docx 模块创建一个新的文档实例，并将该实例发送到 saveDocumentToFile 函数。

```
function generateWordDocument(event) {
  event.preventDefault()
  // Create a new instance of Document for the docx module
  let doc = new Document()
  // Call saveDocumentToFile with the document instance and a filename
  saveDocumentToFile(doc, "New Document.docx")
} 
```

Enter fullscreen mode Exit fullscreen mode

saveDocumentToFile 函数从 docx 模块中创建一个新的 Packer 实例，docx 中的 Packer 用于将 JavaScript 代码转换成一个. docx 文件。然后，我们将我们的文档实例发送到 Packers toBlob 函数，该函数又将我们的文档实例转换成一个 [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob) 对象，一个 [mimeType](https://developer.mozilla.org/en-US/docs/Web/API/MimeType) 被添加到 Blob 中，最后 Blob 被发送到文件保存器模块的 saveAs 功能，该功能将创建文件并提示您保存或打开文件。

```
function saveDocumentToFile(doc, fileName) {
  // Create new instance of Packer for the docx module
  const packer = new Packer()
  // Create a mime type that will associate the new file with Microsoft Word
  const mimeType =
    "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
  // Create a Blob containing the Document instance and the mimeType
  packer.toBlob(doc).then(blob => {
    const docblob = blob.slice(0, blob.size, mimeType)
    // Save the file using saveAs from the file-saver package
    saveAs(docblob, fileName)
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

单击“生成 Word 文档”按钮将创建并保存一个空白的 Word 文档。

### 第五步-创建 Word 文档内容

我们现在可以开始向 Word 文档添加一些内容。我们将简单地添加一个标题、副标题、两个标题和一个长段落，因为这将使我们开始设计文档时更容易理解。

```
function generateWordDocument(event) {
  event.preventDefault()
  let doc = new Document()
  doc.createParagraph("Title")
  doc.createParagraph("Subtitle")
  doc.createParagraph("Heading 1")
  doc.createParagraph("Heading 2")
  doc.createParagraph(
    "Aliquam gravida quam sapien, quis dapibus eros malesuada vel. Praesent tempor aliquam iaculis. Nam ut neque ex. Curabitur pretium laoreet nunc, ut ornare augue aliquet sed. Pellentesque laoreet sem risus. Cras sodales libero convallis, convallis ex sed, ultrices neque. Sed quis ullamcorper mi. Ut a leo consectetur, scelerisque nibh sit amet, egestas mauris. Donec augue sapien, vestibulum in urna et, cursus feugiat enim. Ut sit amet placerat quam, id tincidunt nulla. Cras et lorem nibh. Suspendisse posuere orci nec ligula mattis vestibulum. Suspendisse in vestibulum urna, non imperdiet enim. Vestibulum vel dolor eget neque iaculis ultrices."
  )
  saveDocumentToFile(doc, "New Document.docx")
} 
```

Enter fullscreen mode Exit fullscreen mode

Word 文档现在看起来像这样

[![](img/aa49851313ad9d7b243ee7a507461dc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--38F9CW4n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fdbjmcyg7mldvv6a0jey.png)

### 第六步 Word 文档样式

现在我们有了一些内容，我们可以看看文档的样式。

首先，我们将创建一个主题对象，它将包含我们想要使用的字体和颜色的所有信息。

```
function generateWordDocument(event) {
  event.preventDefault()
  // Create new instance of Document for the docx module
  let doc = new Document()
  doc.theme = {
    font: {
      normal: {
        family: "Calibri",
        color: "303856",
      },
      header: { family: "Calibri Light" },
    },
    title: {
      color: "4ABDAC",
    },
    headings: {
      one: {
        color: "FC4A1A",
      },
      two: {
        color: "F7B733",
      },
    },
  }
  ...
  ...
  // Call saveDocumentToFile withe the document instance and a filename
  saveDocumentToFile(doc, "New Document.docx")
} 
```

Enter fullscreen mode Exit fullscreen mode

创建主题对象后，我们可以开始创建我们的风格。我们将创建 5 种风格

*   自定义标题 1
*   自定义标题 2
*   自定义标题
*   自定义字幕
*   自定义正常

这些应该与我们在步骤 5 中创建的内容很好地结合起来。

你可以在微软 Word [这里](https://support.office.com/en-gb/article/customize-or-create-new-styles-in-word-d38d6e47-f6fc-48eb-a607-1eb120dec563)
了解更多关于样式的知识

```
function generateWordDocument(event) {
  event.preventDefault()
  // Create new instance of Document for the docx module
  let doc = new Document()
  ...
  ...
  doc.Styles.createParagraphStyle("customHeading1", "Custom Heading 1")
    .basedOn("Heading 1")
    .next("Normal")
    .quickFormat()
    .font(doc.theme.font.header.family)
    .size(32)
    .bold()
    .color(doc.theme.headings.one.color)
    .spacing({ after: 250 })

  doc.Styles.createParagraphStyle("customHeading2", "Custom Heading 2")
    .basedOn("Heading 2")
    .next("Normal")
    .quickFormat()
    .font(doc.theme.font.header.family)
    .size(26)
    .bold()
    .color(doc.theme.headings.two.color)
    .spacing({ after: 150 })

  doc.Styles.createParagraphStyle("customTitle", "Custom Title")
    .basedOn("Title")
    .next("Normal")
    .quickFormat()
    .font(doc.theme.font.header.family)
    .size(56)
    .bold()
    .color(doc.theme.font.normal.color)
    .spacing({ after: 250 })

  doc.Styles.createParagraphStyle("customSubtitle", "Custom Subtitle")
    .basedOn("Subtitle")
    .next("Normal")
    .quickFormat()
    .font(doc.theme.font.header.family)
    .size(22)
    .color(doc.theme.font.normal.color)
    .spacing({ after: 150 })

  doc.Styles.createParagraphStyle("customNormal", "Custom Normal")
    .basedOn("Normal")
    .quickFormat()
    .font(doc.theme.font.normal.family)
    .size(20)
    .color(doc.theme.font.normal.color)
    .spacing({ after: 150 })
  ...
  ...
  // Call saveDocumentToFile withe the document instance and a filename
  saveDocumentToFile(doc, "New Document.docx")
} 
```

Enter fullscreen mode Exit fullscreen mode

有了我们的风格，我们可以将它们应用到我们的内容中。

```
function generateWordDocument(event) {
  event.preventDefault()
  // Create new instance of Document for the docx module
  let doc = new Document()
  ...
  ...
  doc.createParagraph("Title").style("customTitle")
  doc.createParagraph("Subtitle").style("customSubtitle")
  doc.createParagraph("Heading 1").style("customHeading1")
  doc.createParagraph("Heading 2").style("customHeading2")
  doc
    .createParagraph(
      "Aliquam gravida quam sapien, quis dapibus eros malesuada vel. Praesent tempor aliquam iaculis. Nam ut neque ex. Curabitur pretium laoreet nunc, ut ornare augue aliquet sed. Pellentesque laoreet sem risus. Cras sodales libero convallis, convallis ex sed, ultrices neque. Sed quis ullamcorper mi. Ut a leo consectetur, scelerisque nibh sit amet, egestas mauris. Donec augue sapien, vestibulum in urna et, cursus feugiat enim. Ut sit amet placerat quam, id tincidunt nulla. Cras et lorem nibh. Suspendisse posuere orci nec ligula mattis vestibulum. Suspendisse in vestibulum urna, non imperdiet enim. Vestibulum vel dolor eget neque iaculis ultrices."
    )
    .style("customNormal")
  // Call saveDocumentToFile withe the document instance and a filename
  saveDocumentToFile(doc, "New Document.docx")
} 
```

Enter fullscreen mode Exit fullscreen mode

现在单击“生成 Word 文档”按钮将创建一个类似于以下内容的 Word 文档

[![](img/74be620ce19f313da8270da1671cdf26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VdKG991Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/660px3xaweqs8uwwnvip.png)

## 结论

通过使用几个优秀的软件包，我们可以很容易地创建一个漂亮的 Word 文档，只使用客户端 JavaScript。Docx 非常强大，本文只介绍了让您使用一个简单的 Word 文档的基础知识。

## 完整的例子

完整的代码示例可以在[这里](https://codesandbox.io/s/createworddocument-qnf4u)找到