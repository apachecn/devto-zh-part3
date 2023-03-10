# 使用 C#导出注释和检索文档页面。NET 注释器 API

> 原文：<https://dev.to/groupdocs/export-annotations-and-retrieve-document-pages-using-c-net-annotator-api-3mpm>

最新每月发行的。NET 文档注释器 API 允许应用程序开发人员在注释文档或图像的同时，使用或不使用注释来恢复文档页面，导出特定的注释类型和页面范围。NET 平台。group docs 18.12 版本的其他亮点。的注释。NET 是重要的改进，例如为组合形状添加了文本替换，并修复了一些问题—[http://bit.ly/2T5VBeQ](http://bit.ly/2T5VBeQ)

以下代码示例说明了如何从 JSON 文件中获取注释并将它们导出到 PDF 文档中:

`// Create instance of annotator.
AnnotationConfig cfg = CommonUtilities.GetConfiguration();
AnnotationImageHandler annotator = new AnnotationImageHandler(cfg);
// Get file stream
Stream manifestResourceStream = new FileStream(CommonUtilities.MapSourceFilePath(filePath), FileMode.Open, FileAccess.ReadWrite);
List<AnnotationInfo> annotations = new List<AnnotationInfo>();
Stream stream = annotator.ExportAnnotationsToDocument(manifestResourceStream,annotations, DocumentType.Pdf);
// Save result stream to file.
using (FileStream fileStream = new FileStream(CommonUtilities.MapDestinationFilePath("Annotated.pdf"), FileMode.Create))
{
byte[] buffer = new byte[stream.Length];
stream.Seek(0, SeekOrigin.Begin);
stream.Read(buffer, 0, buffer.Length);
fileStream.Write(buffer, 0, buffer.Length);
fileStream.Close();
}`

YouTube 视频教程。NET API-[http://bit.ly/2Sod3vn](http://bit.ly/2Sod3vn)