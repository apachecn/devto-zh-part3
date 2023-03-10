# 用木偶师从 HTML 生成 PDF

> 原文：<https://dev.to/damcosset/generate-a-pdf-from-html-and-back-on-the-front-5ff5>

# 简介

这是一个令人沮丧的帖子，我刚刚花了几个小时做了一些事情，我终于找到了一个可行的解决方案。我学到了不少东西，但我觉得这不应该花我那么多时间...

无论如何，目标是从 HTML 生成 PDF，然后将其发送回浏览器，以便用户可以下载。我尝试了很多不同的东西，很可能我的解决方案不是最优雅的，也不是最快的，但是去他妈的，它是有效的。

我认为这篇文章是一个存放这个解决方案的地方，以防我将来忘记它。我知道去哪里找。让我们直接进入实际的解决方案。

# 解！

## 前端

让我们从前端开始。

```
const downloadPDF = () => {
        fetch('/api/invoices/create-pdf', {
            data: {
                invoiceDetails,
                invoiceSettings,
                itemsDetails,
                organisationInfos,
                otherDetails,
                clientDetails
            },
            method: 'POST'
        }).then(res => {
            return res
                .arrayBuffer()
                .then(res => {
                    const blob = new Blob([res], { type: 'application/pdf' })
                    saveAs(blob, 'invoice.pdf')
                })
                .catch(e => alert(e))
        })
    } 
```

这是做所有事情的函数。在我的案例中，我们正在生成发票。

1)用 POST 方法进行提取。这是我们用适当的数据生成 PDF 并在服务器上生成 PDF 的部分。(随后是服务器代码)

3)我们得到的响应需要转换成 arraybuffer。

4)我们用新的 Blob()构造函数创建一个 Blob(二进制大对象)。Blob 将 iterable 作为第一个参数。请注意，我们的反应变成了数组缓冲器被方形的 braquets(*【RES】*)包围。要创建一个可以作为 PDF 读取的 blob，数据需要可以迭代成二进制形式(我认为...).另外，请注意类型*应用程序/pdf* 。

5)最后，我使用*文件保存器*包中的 saveAs 函数在前端创建文件！

## 后端

这里是后端的东西。有一个完整的快递申请和一切。我只是向你们展示控制器，这两个方法在这个 PDF 问题中的位置。

```
module.exports = {
    createPDF: async function(req, res, next) {
        const content = fs.readFileSync(
            path.resolve(__dirname, '../invoices/templates/basic-template.html'),
            'utf-8'
        )
        const browser = await puppeteer.launch({ headless: true })
        const page = await browser.newPage()
        await page.setContent(content)
        const buffer = await page.pdf({
            format: 'A4',
            printBackground: true,
            margin: {
                left: '0px',
                top: '0px',
                right: '0px',
                bottom: '0px'
            }
        })
                await browser.close()
        res.end(buffer)
    }
} 
```

1)我使用*木偶师*从 HTML 内容创建一个 PDF。HTML 内容是从我用 *readFileSync* 简单获取的 HTML 文件中读取的

2)我们存储由 *page.pdf()* 返回的缓冲数据，并将其返回给前端。这是后来转换成 arraybuffer 的响应。

# 搞定

嗯，看看代码，现在看起来真的更容易了，因为当我试图解决这个问题时，它实际上是这样做的。我花了将近 10 个小时才找到一个合适的答案。十个小时！！！！

自我提醒:如果你感到沮丧，离开电脑，呼吸一些新鲜空气，稍后再回来...

快乐编码< 3