# 使用 C#检索 Outlook 数据文件中包含的文件夹。NET API

> 原文：<https://dev.to/groupdocs/retrieve-folders-contained-in-outlook-data-files-using-c-net-api-4n8g>

借助最新版本的，应用程序开发人员可以从 OST 和 PST Outlook 数据文件中获取文件夹和子文件夹列表，并将这些文件夹中的邮件呈现为 PDF、HTML 和图像格式。NET 文档呈现 API。此外，在中呈现 Microsoft Excel 工作表时，您可以通过编程方式忽略空列。使用 GroupDocs 处理大文件时，可以减少处理时间并优化内存使用。查看器。NET-[http://bit.ly/2UNFCDL](http://bit.ly/2UNFCDL)

有关如何在应用程序中执行这些任务的更多帮助，请参考以下代码示例/代码片段。

下面的代码片段显示了如何检索文件夹列表:

`// Setup GroupDocs.Viewer config
ViewerConfig config = Utilities.GetConfigurations();
// Create image handler
ViewerImageHandler imageHandler = new ViewerImageHandler(config);
string guid = DocumentName;
// Get Outlook document info
OutlookDocumentInfoContainer documentInfoContainer = imageHandler.GetDocumentInfo(guid) as OutlookDocumentInfoContainer;
foreach (string folderName in documentInfoContainer.Folders)
Console.WriteLine("Folder name: {0}", folderName);`

以下是检索子文件夹列表的方法:

`// Setup GroupDocs.Viewer config
ViewerConfig config = Utilities.GetConfigurations();
// Create image handler
ViewerImageHandler imageHandler = new ViewerImageHandler(config);
string guid = DocumentName;
// Create option object with specified folder name
DocumentInfoOptions options = new DocumentInfoOptions();
options.OutlookOptions.FolderName = "Inbox";
// Get outlook document info
OutlookDocumentInfoContainer documentInfoContainer = imageHandler.GetDocumentInfo(guid, options) as OutlookDocumentInfoContainer;
foreach (string folderName in documentInfoContainer.Folders)
Console.WriteLine("Folder name: {0}", folderName);`

下面的代码片段演示了如何将任何检索到的文件夹中的电子邮件呈现为图像:

`// Setup GroupDocs.Viewer config
ViewerConfig config = Utilities.GetConfigurations();
// Create image handler
ViewerImageHandler imageHandler = new ViewerImageHandler(config);
string guid = DocumentName;
// Create image options with specified folder name (use HtmlOptions to render into HTML)
ImageOptions options = new ImageOptions();
options.OutlookOptions.FolderName = "Inbox\\Sub Folder 1";
// Render document into image (List<PageHtml> is returned when rendering into HTML)
List<PageImage> pages = imageHandler.GetPages(guid, options);
foreach (PageImage page in pages)
{
// Save each image at disk
Utilities.SaveAsImage(page.PageNumber + "_" + DocumentName, page.Stream);
}`

要将邮件呈现为 PDF，请参考以下代码示例:

`// Setup GroupDocs.Viewer config
ViewerConfig config = Utilities.GetConfigurations();
// Create image handler
ViewerImageHandler imageHandler = new ViewerImageHandler(config);
string guid = DocumentName;
// Create pdf options with specified folder name
PdfFileOptions options = new PdfFileOptions();
options.OutlookOptions.FolderName = "Inbox";
// Get pdf document
FileContainer fileContainer = imageHandler.GetPdfFile(guid, options);
//Save file
Utilities.SaveFile(guid, fileContainer.Stream);`

GroupDocs API 的 YouTube 视频教程—[http://bit.ly/2UKFx3L](http://bit.ly/2UKFx3L)