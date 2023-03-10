# 用 Node.js 和 Puppeteer 从 HTML 生成 PDF

> 原文：<https://dev.to/bmz1/generating-pdf-from-html-with-nodejs-and-puppeteer-5ln>

*最初发布于[blog.risingstack.com](https://blog.risingstack.com/pdf-from-html-node-js-puppeteer/)2019 年 2 月 5 日。*

在这篇文章中，我将展示如何使用 Node.js，Puppeteer，headless Chrome & Docker 从一个风格化的 React 页面生成一个 PDF 文档。

背景:几个月前， [RisingStack](https://risingstack.com/) 的一个客户要求我们开发一个功能，用户可以请求 PDF 格式的 React 页面。该页面基本上是一个数据可视化的患者报告/结果，包含许多 SVG。此外，还有一些特殊的请求来操作布局，并对 HTML 元素进行一些重新排列。因此，与最初的 React 页面相比，PDF 应该有不同的样式和附加内容。

由于任务比简单的 CSS 规则所能解决的要复杂一些，我们首先探索了可能的实现。基本上，我们找到了 3 个主要的解决方案。这篇博文将带你了解这些可能性和最终的实现。

在我们开始之前的一个个人评论:这是相当麻烦的，所以系好安全带！

## 目录:

*   [客户端还是后端？](#clientside)
*   [选项 1:从 DOM 制作截图](#option1)
*   [选项 2:仅使用 PDF 库](#option2)
*   [最终选择 3:木偶师，无头 Chrome，带 Node.js](#option3)
    *   [风格操作](#style)
    *   [向客户端发送文件并保存](#file)
*   [使用带对接器的木偶师](#puppeteer)
*   [选项 3 +1: CSS 打印规则](#option31)
*   [总结](#summary)

## 客户端还是服务器端？

在客户端和服务器端都可以生成 PDF 文件。然而，让后端处理它可能更有意义，因为您不想耗尽用户浏览器所能提供的所有资源。尽管如此，我还是会展示这两种方法的解决方案。

## 选项一:从 DOM 中截图

乍一看，这个解决方案似乎是最简单的，事实证明也是如此，但它有自己的局限性。如果您没有特殊需求，比如 PDF 中的可选或可搜索文本，这是一个很好的简单方法来生成一个。

这个方法简单明了:从页面创建一个截屏，并将其放入 PDF 文件中。很简单。我们为此方法使用了两个包:

*   [Html2canvas](https://html2canvas.hertzen.com/) ，从 DOM 制作截图
*   [jsPdf](https://github.com/MrRio/jsPDF) ，生成 Pdf 的库

开始编码吧。

```
npm install html2canvas jspdf 
```

```
import html2canvas from 'html2canvas'
import jsPdf from 'jspdf'

function printPDF () {
    const domElement = document.getElementById('your-id')
    html2canvas(domElement, { onclone: (document) => {
      document.getElementById('print-button').style.visibility = 'hidden'
}})
    .then((canvas) => {
        const img = canvas.toDataURL('image/png')
        const pdf = new jsPdf()
        pdf.addImage(imgData, 'JPEG', 0, 0, width, height)
        pdf.save('your-filename.pdf')
}) 
```

就是这样！

一定要看一看`html2canvas` `onclone`方法。当您需要在拍照前快速拍摄快照并操作 DOM(例如隐藏打印按钮)时，它会非常方便。我可以看到这个包的很多用例。不幸的是，我们的不是，因为我们需要在后端处理 PDF 创建。

## 选项 2:仅使用 PDF 库

NPM 上有几个用于这个目的的库，比如 jsPDF(如上所述)或 [PDFKit](https://www.npmjs.com/package/pdfkit) 。它们的问题是，如果我想使用这些库，我必须重新创建页面结构。这肯定会损害可维护性，因为我需要将所有后续更改应用到 PDF 模板和 React 页面。
看看下面的代码。您需要自己手动创建 PDF 文档。现在，您可以遍历 DOM 并找出如何将每个元素转换成 PDF 元素，但这是一项繁琐的工作。一定有更简单的方法。

```
doc = new PDFDocument
doc.pipe fs.createWriteStream('output.pdf')
doc.font('fonts/PalatinoBold.ttf')
   .fontSize(25)
   .text('Some text with an embedded font!', 100, 100)

doc.image('path/to/image.png', {
   fit: [250, 300],
   align: 'center',
   valign: 'center'
});

doc.addPage()
   .fontSize(25)
   .text('Here is some vector graphics...', 100, 100)

doc.end() 
```

这个片段来自 PDFKit 文档。但是，如果您的目标是一个 PDF 文件，而不是一个已经存在的(并且不断变化的)HTML 页面的转换，那么它会很有用。

## 最终选项 3:木偶师，带 Node.js 的无头 Chrome

什么是[木偶师](https://github.com/GoogleChrome/puppeteer)？文件上说:

> Puppeteer 是一个节点库，它提供了一个高级 API 来控制 Chrome 或 DevTools 协议上的 Chrome。默认情况下，Puppeteer 是无头运行的，但是可以配置为运行完整的(非无头)Chrome 或 Chrome。
> 这基本上是一个浏览器，你可以从 Node.js 运行它。如果你阅读文档，它对 Puppeteer 的第一句话是，你可以用它来**生成页面的截图和 PDFs】。太棒了。那正是我们要找的。
> 让我们用`npmi i puppeteer`安装木偶师，并实现我们的用例。**

```
const puppeteer = require('puppeteer')

async function printPDF() {
  const browser = await puppeteer.launch({ headless: true });
  const page = await browser.newPage();
  await page.goto('https://blog.risingstack.com', {waitUntil: 'networkidle0'});
  const pdf = await page.pdf({ format: 'A4' });

  await browser.close();
  return pdf
}) 
```

这是一个简单的功能，导航到一个 URL 并生成该网站的 PDF 文件。首先，我们启动浏览器(PDF 生成仅在 headless 模式下受支持)，然后我们打开一个新页面，设置视窗，并导航到提供的 URL。

设置`waitUntil: ‘networkidle0’`选项意味着当至少 500 毫秒没有网络连接时，木偶师认为导航结束。(查看 [API 文档](https://github.com/GoogleChrome/puppeteer/blob/v1.11.0/docs/api.md)了解更多信息)。)

之后，我们将 PDF 保存到一个变量中，关闭浏览器并返回 PDF。

注意:`page.pdf`方法接收一个`options`对象，在这里您也可以用“path”选项将文件保存到磁盘。如果没有提供路径，PDF 将不会保存到磁盘，您将获得一个缓冲区。稍后，我会讨论如何处理它。)

如果您需要首先登录以从受保护的页面生成 PDF，首先您需要导航到登录页面，检查表单元素的 ID 或名称，填写它们，然后提交表单:

```
await page.type('#email', process.env.PDF_USER)
await page.type('#password', process.env.PDF_PASSWORD)
await page.click('#submit') 
```

始终将登录凭证存储在环境变量中，不要硬编码它们！

### 风格操纵

木偶师也有这种风格操作的解决方案。您可以在生成 PDF 之前插入样式标签，Puppeteer 将使用修改后的样式生成一个文件。

```
await page.addStyleTag({ content: '.nav { display: none} .navbar { border: 0px} #print-button {display: none}' }) 
```

### 发送文件到客户端并保存

好了，现在你已经在后端生成了一个 PDF 文件。现在该怎么办？正如我上面提到的，如果你不把文件保存到磁盘，你会得到一个缓冲区。您只需要将具有适当内容类型的缓冲区发送到前端。

```
printPDF.then(pdf => {
    res.set({ 'Content-Type': 'application/pdf', 'Content-Length': pdf.length })
    res.send(pdf) 
```

现在，您可以简单地向服务器发送请求，以获取生成的 PDF。

```
function getPDF() {
 return axios.get(`${API_URL}/your-pdf-endpoint`, {
   responseType: 'arraybuffer',
   headers: {
     'Accept': 'application/pdf'
   }
 }) 
```

一旦你发送了请求，缓冲区应该开始下载。现在最后一步是将缓冲区转换成 PDF 文件。

```
savePDF = () => {
    this.openModal(‘Loading…’) // open modal
   return getPDF() // API call
     .then((response) => {
       const blob = new Blob([response.data], {type: 'application/pdf'})
       const link = document.createElement('a')
       link.href = window.URL.createObjectURL(blob)
       link.download = `your-file-name.pdf`
       link.click()
       this.closeModal() // close modal
     })
   .catch(err => /** error handling **/)
 } 
```

```
<button onClick={this.savePDF}>Save as PDF</button> 
```

就是这样！如果您单击“保存”按钮，浏览器将保存 PDF。

## 使用傀儡师配合 Docker

我认为这是实现中最棘手的部分——所以让我为你节省几个小时的谷歌搜索时间。
官方文档称*“在 Docker 中安装和运行无头浏览器可能会很棘手”*。官方文档有一个[故障排除](https://github.com/GoogleChrome/puppeteer/blob/master/docs/troubleshooting.md#running-puppeteer-in-docker)部分，在撰写本文时，你可以找到所有关于使用 Docker 安装木偶师的必要信息。
如果你在 Alpine 图像上安装了木偶师，确保你向下滚动到[页面的这一部分](https://github.com/GoogleChrome/puppeteer/blob/master/docs/troubleshooting.md#running-on-alpine)。否则，您可能会忽略这样一个事实，即您无法运行最新版本的 Puppeteer，并且您还需要使用一个标志来禁用 shm 用法:

```
const browser = await puppeteer.launch({
  headless: true,
  args: ['--disable-dev-shm-usage']
}); 
```

否则，Puppeteer 子进程可能会在正常启动之前就耗尽内存。更多信息，请点击上面的故障排除链接。

## 选项 3 + 1: CSS 打印规则

有人可能认为，从开发人员的角度来看，简单地使用 CSS 打印规则很容易。没有 NPM 模块，只有纯 CSS。但是当涉及到跨浏览器兼容性时，它们的表现如何呢？当选择 CSS 打印规则时，你必须在每个浏览器中测试结果，以确保它提供相同的布局，而且不是 100%的。
例如，在给定元素后插入一个 break 不能被认为是一个深奥的用例，然而你可能会惊讶于你需要使用变通方法[来在 Firefox](https://developer.mozilla.org/en-US/docs/Web/CSS/break-after#Browser_compatibility) 中实现它。
除非你是一个久经沙场的 CSS 魔术师，在创建可打印页面方面有丰富的经验，否则这可能会很耗时。如果你能保持打印样式表的简单，打印规则是很棒的。让我们看一个例子。

```
@media print {
    .print-button {
        display: none;
    }

    .content div {
        break-after: always;
    }
} 
```

上面的 CSS 隐藏了打印按钮，并在每一个`div`后插入一个分页符。有一篇[的伟大文章](https://www.smashingmagazine.com/2018/05/print-stylesheets-in-2018/)总结了你可以用打印规则做什么，以及它们有什么困难，包括浏览器兼容性。
从各方面考虑，如果你想从一个不太复杂的页面制作一个 PDF，CSS 打印规则是非常棒和有效的。

## 摘要:用 Node.js 和 Puppeteer 从 HTML 生成 PDF

因此，让我们快速浏览一下我们在这里讨论过的从 HTML 页面生成 PDF 文件的选项:

*   **来自 DOM 的截图**:当您需要从页面创建快照(例如创建缩略图)时，这可能很有用，但是当您有大量数据要处理时，这就不够用了。
*   **只使用 PDF 库**:如果您需要从头开始以编程方式创建 PDF 文件，这是一个完美的解决方案。否则，您需要维护 HTML 和 PDF 模板，这是绝对不行的。
*   尽管让它在 Docker 上工作相对困难，但它为我们的用例提供了最好的结果，也是最容易编写代码的。
*   CSS 打印规则:如果你的用户受过足够的教育，知道如何打印到一个文件，并且你的页面相对简单，这可能是最简单的解决方案。如你所见，我们的情况并非如此。印刷快乐！