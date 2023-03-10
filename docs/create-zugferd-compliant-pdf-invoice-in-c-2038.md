# 用 C#创建符合 ZUGFeRD 的 PDF 发票

> 原文：<https://dev.to/syncfusion/create-zugferd-compliant-pdf-invoice-in-c-2038>

[Syncfusion PDF 库](https://www.syncfusion.com/pdf-framework/net/pdf-library)现在支持用 C#创建符合 [ZUGFeRD](https://www.ferd-net.de/) 的 PDF 发票。ZUGFeRD (Zentraler 用户指南 des Forums Elektronische rech nung Deutschland)是一种德国计价标准，用于公共和私营部门的结构化数据交换。ZUGFeRD PDF 与 PDF/A-3 兼容，并带有专门为长期存档设计的 XML 文件附件。使用 ZUGFeRD PDF 制作的发票包含带有文本、图形和图像的人类可读发票，以及 XML 格式的机器可读结构化发票数据。

该开票标准具有多配置文件一致性:

*   基本:表示简单发票的结构化数据，您可以自由文本形式包括附加信息。
*   舒适:代表全自动发票处理的结构化数据。
*   扩展:表示用于跨不同行业段交换发票的附加结构化数据。

在本文中，让我们使用 C#创建一个基本的 ZUGFeRD 发票，如下图所示。

[![ZUGFeRD PDF Invoice](img/90a151a444fc5fe2d38baaf06e824e81.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G6bagbvK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/ZUGFeRDPDFInvoice-2.png)

火车票 PDF 发票

## 创建 ZUGFeRD 发票

创建符合 PDF/A-3b 的 PDF 文档，并将 ZUGFeRD 符合性级别设置为基本。

```
//Create PDF with PDF/A-3b conformance. 

PdfDocument document = new PdfDocument(PdfConformanceLevel.Pdf_A3B); 

//Set ZUGFeRD profile. 

document.ZugferdConformanceLevel = ZugferdConformanceLevel.Basic; 
```

现在创建你想要在文档中使用的字体和笔刷。声明边距、行距和高度的值，以创建外观丰富的 ZUGFeRD PDF 发票。

```
//Create border color. 
PdfColor borderColor = new PdfColor(Color.FromArgb(255, 142, 170, 219)); 
PdfBrush lightBlueBrush = new PdfSolidBrush(new PdfColor(Color.FromArgb(255, 91, 126, 215))); 
PdfBrush darkBlueBrush = new PdfSolidBrush(new PdfColor(Color.FromArgb(255, 65, 104, 209))); 
PdfBrush whiteBrush = new PdfSolidBrush(new PdfColor(Color.FromArgb(255, 255, 255, 255))); 
PdfPen borderPen = new PdfPen(borderColor, 1f); 
//Create TrueType font. PdfTrueTypeFont headerFont = new PdfTrueTypeFont(new Font("Arial", 30, System.Drawing.FontStyle.Regular), true); 
PdfTrueTypeFont arialRegularFont = new PdfTrueTypeFont(new Font("Arial", 9, System.Drawing.FontStyle.Regular), true); 
PdfTrueTypeFont arialBoldFont = new PdfTrueTypeFont(new Font("Arial", 11, System.Drawing.FontStyle.Bold), true); 
const float margin = 30; 
const float lineSpace = 7; 
const float headerHeight = 90; 
```

这里，大多数参数(字体大小、边距、行距和高度)都是基于 A4 页面大小进行调整的。

很明显，我们可以根据页面的大小来调整这些参数，但是这会使这个例子稍微复杂一些。

### 添加表头和买家信息

使用 Syncfusion PDF 库从头开始创建 PDF 非常简单，并提供了几种在文档中放置元素的方法。在本例中，我们将发票标题和买家信息放在左边，金额、发票号和日期放在右边。

以下代码片段用于创建包含基本信息的发票标题。

```
//Add page to the PDF. 
PdfPage page = document.Pages.Add(); 

PdfGraphics graphics = page.Graphics; 

//Get the page width and height. 
float pageWidth = page.GetClientSize().Width; 
float pageHeight = page.GetClientSize().Height; 

//Draw page border 

graphics.DrawRectangle(borderPen, new RectangleF(0, 0, pageWidth, pageHeight)); 

//Fill the header with light Brush. 

graphics.DrawRectangle(lightBlueBrush, new RectangleF(0, 0, pageWidth, headerHeight)); 

RectangleF headerAmountBounds = new RectangleF(400, 0, pageWidth - 400, headerHeight); 
graphics.DrawString("INVOICE", headerFont, whiteBrush, new PointF(margin, headerAmountBounds.Height / 3)); 

graphics.DrawRectangle(darkBlueBrush, headerAmountBounds);

graphics.DrawString("Amount", arialRegularFont, whiteBrush, headerAmountBounds, new PdfStringFormat(PdfTextAlignment.Center, PdfVerticalAlignment.Middle)); 

PdfTextElement textElement = new PdfTextElement("Invoice Number: 2058557939", arialRegularFont); 

PdfLayoutResult layoutResult = textElement.Draw(page, new PointF(headerAmountBounds.X - margin, 120)); 

textElement.Text = "Date : " + DateTime.Now.ToString("dddd dd, MMMM yyyy"); 
textElement.Draw(page, new PointF(layoutResult.Bounds.X, layoutResult.Bounds.Bottom + lineSpace)); 

textElement.Text = "Bill To:"; layoutResult = textElement.Draw(page, new PointF(margin, 120)); 

textElement.Text = "Abraham Swearegin,"; 

layoutResult = textElement.Draw(page, new PointF(margin, layoutResult.Bounds.Bottom + lineSpace)); 

textElement.Text = "United States, California, San Mateo,"; 

layoutResult = textElement.Draw(page, new PointF(margin, layoutResult.Bounds.Bottom + lineSpace)); 

textElement.Text = "9920 BridgePointe Parkway,"; 

layoutResult = textElement.Draw(page, new PointF(margin, layoutResult.Bounds.Bottom + lineSpace)); 

textElement.Text = "9365550136"; 

layoutResult = textElement.Draw(page, new PointF(margin, layoutResult.Bounds.Bottom + lineSpace)); 
```

[![PDF invoice with header and buyer information](img/5de4800656435c89e9c7028df8edcc1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--25Vqk3SX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/ZUGFeRDHeader.png)

带有标题和买方信息的 PDF 发票

### 添加发票数据

然后，我们使用 [Syncfusion PDF 表格](https://www.syncfusion.com/pdf-framework/net/pdf-library/pdf-tables)将数据库中的发票数据添加到五列表格格式中。我们可以只使用一行代码来应用表格外观，就像在 Microsoft Word 表格中一样。

```
PdfGrid grid = new PdfGrid(); 

grid.DataSource = GetProductReport(); 

grid.Columns[1].Width = 150; 

grid.Style.Font = arialRegularFont; 

grid.Style.CellPadding.All = 5; 

grid.ApplyBuiltinStyle(PdfGridBuiltinStyle.ListTable4Accent5); 

layoutResult = grid.Draw(page, new PointF(0, layoutResult.Bounds.Bottom + 40));
textElement.Text = "Grand Total: "; 

textElement.Font = arialBoldFont; 

layoutResult = textElement.Draw(page, new PointF(headerAmountBounds.X - 40, layoutResult.Bounds.Bottom + lineSpace)); 

float totalAmount = GetTotalAmount(grid); 

textElement.Text = "$" + totalAmount.ToString(); 

layoutResult = textElement.Draw(page, new PointF(layoutResult.Bounds.Right + 4, layoutResult.Bounds.Y)); 

graphics.DrawString("$" + totalAmount.ToString(), arialBoldFont, whiteBrush, new RectangleF(400, lineSpace, pageWidth - 400, headerHeight + 15), new PdfStringFormat(PdfTextAlignment.Center, PdfVerticalAlignment.Middle)); 
```

[![PDF invoice with table](img/a08fb5857569a822a3199042322affcd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nkOPKCzn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/ZUGFeRDTable.png)

带表格的 PDF 发票

### 添加卖家信息

最后，我们用虚线创建发票的页脚，并添加卖方的地址。这样，我们已经使用 C#从零开始成功地创建了一个外观丰富的 PDF 发票。

```
borderPen.DashStyle = PdfDashStyle.Custom; 

borderPen.DashPattern = new float[] { 3, 3 }; 

PdfLine line = new PdfLine(borderPen, new PointF(0, 0), new PointF(pageWidth, 0));

layoutResult = line.Draw(page, new PointF(0, pageHeight - 100)); 

textElement.Text = "800 Interchange Blvd."; 

textElement.Font = arialRegularFont; 

layoutResult = textElement.Draw(page, new PointF(margin, layoutResult.Bounds.Bottom + (lineSpace * 3))); 

textElement.Text = "Suite 2501, Austin, TX 78721"; 

layoutResult = textElement.Draw(page, new PointF(margin, layoutResult.Bounds.Bottom + lineSpace)); 

textElement.Text = "Any Questions? support@adventure-works.com"; 

layoutResult = textElement.Draw(page, new PointF(margin, layoutResult.Bounds.Bottom + lineSpace)); 
```

[![PDF invoice with seller information in footer](img/9312af280a378077d69fb019bffe678b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hoGhOpjl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/ZUGFeRDFooter.png)

页脚有卖家信息的 PDF 发票

## 创建列车总台 XML

ZUGFeRD XML 基于由联合国贸易便利化和电子商务中心(UN/CEFACT)开发的[跨行业发票](http://tfig.unece.org/contents/cross-industry-invoice-cii.htm) (CII)。在本节中，我们将使用 [ZUGFeRD XML 创建助手类文件](http://www.syncfusion.com/downloads/support/directtrac/general/ze/ZUGFeRDXMLHelper-1727879939)创建一个基本概要文件 ZUGFeRD XML。下面的代码片段将使用 helper 类为创建的发票数据创建一个 ZUGFeRD XML。

```
//Create ZUGFeRD Invoice. 
ZugferdInvoice invoice = new ZugferdInvoice("2058557939", DateTime.Now, CurrencyCodes.USD); 

//Set ZUGFeRD profile to basic 
invoice.Profile = ZugferdProfile.Basic; 

//Add buyer details. 
invoice.Buyer = new UserDetails { ID = "Abraham_12", Name = "Abraham Swearegin", ContactName = "Swearegin", City = "United States, California", Postcode = "9920", Country = CountryCodes.US, Street = "9920 BridgePointe Parkway" }; 

//Add seller details 
invoice.Seller = new UserDetails { ID = "Adventure_123", Name = "AdventureWorks", ContactName = "Adventure support", City = "Austin,TX", Postcode = "78721", Country = CountryCodes.US, Street = "800 Interchange Blvd" }; 

IEnumerable products = GetProductReport(); 

foreach (Product product in products) invoice.AddProduct(product);

invoice.TotalAmount = totalAmount; 

MemoryStream zugferdXML = new MemoryStream(); 

invoice.Save(zugferdXML); 
```

## 在 PDF 中嵌入 ZUGFeRD XML

创建 ZUGFeRD XML 后，将其嵌入 PDF/A-3b 文档中。根据 ZUGFeRD 指南，嵌入的 XML 文件名应该是 ZUGFeRD-invoice.xml。附件和文档的关系必须是交替的。在 PDF/A-3b 中，这用 AFRelationship 键表示。

```
//Attach ZUGFeRD XML to PDF 
PdfAttachment attachment = new PdfAttachment("ZUGFeRD-invoice.xml", zugferdXML); 

attachment.Relationship = PdfAttachmentRelationship.Alternative; 

attachment.ModificationDate = DateTime.Now; 

attachment.Description = "ZUGFeRD-invoice"; 

attachment.MimeType = "application/xml"; 

document.Attachments.Add(attachment); 

document.Save("ZUGFeRDInvoice.pdf"); 

document.Close(true); 
```

演示使用 Syncfusion PDF 库创建 ZUGFeRD 发票的示例可从 [ZUGFeRD-invoice.zip](http://www.syncfusion.com/downloads/support/directtrac/general/ze/ZUGFeRDInvoice-2119355011) 下载。

## 结论

总之，Syncfusion。NET PDF 库提供了一种使用 C#创建 ZUGFeRD 发票的简单方法。借助 Syncfusion PDF 库，您还可以从合规发票中提取 ZUGFeRD XML。您还应该尝试其他类型的 ZUGFeRD 配置文件。

要使用 C#功能评估我们的 ZUGFeRD PDF 发票，请尝试我们的[在线演示](https://ej2.syncfusion.com/aspnetcore/PDF/Zugferd#/material)。花点时间细读一下[文档](https://help.syncfusion.com/file-formats/pdf/working-with-zugferd-invoice)，在那里你会找到其他选项和特性，都附有代码示例。

如果您对这些功能有任何疑问或需要澄清，请在下面的评论中告诉我们。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums)或 [Direct-Trac](https://www.syncfusion.com/support/directtrac/) 联系我们。我们很乐意为您提供帮助！

如果你喜欢这篇文章，你可能也会喜欢:

*   【博文】[在 Linux Docker 中使用 ASP.NET 核心进行 HTML 到 PDF 的转换](https://blog.syncfusion.com/post/html-to-pdf-conversion-using-asp-net-core-in-linux-docker.aspx)
*   [博客文章] [使用 C#编辑 pdf 的简单方法](https://blog.syncfusion.com/post/easy-ways-to-redact-pdfs-using-c.aspx)
*   【博文】[用 C#压缩 PDF 文件的 7 种方法，VB.NET](https://blog.syncfusion.com/post/7-ways-to-compress-pdf-files.aspx)
*   [电子书][ASP.NET 简洁地核心](https://www.syncfusion.com/ebooks/asp_net_core_succinctly)

帖子[用 C#](https://blog.syncfusion.com/post/create-zugferd-compliant-pdf-invoices-in-c.aspx) 创建符合 ZUGFeRD 的 PDF 发票最先出现在 [Syncfusion 博客](https://blog.syncfusion.com)上。