# 使用 Java API 熟练操作密码保护文档中的元数据

> 原文：<https://dev.to/groupdocs/proficiently-manipulate-metadata-in-your-password-protected-documents-using-java-api-2f73>

如今，普通互联网用户普遍使用在线文件共享服务，人们每天都依赖这种服务来共享他们的文档。我们在互联网上免费分发我们的法律、专业、图像和其他类型的文件，硬拷贝很快成为过去。这也意味着我们经常会遇到加密或密码保护的文件，这确保了文档的安全性和最终用户的满意度。

软件开发过程已经发展到包括用于在受保护的文档中操作各种类型的数据的算法。GrouDocs。Java API 的元数据就是这样一种解决方案，它为应用程序开发人员提供了在 Java 平台上使用受密码保护的 Microsoft Word、Excel、PowerPoint 和 PDF 文档的元数据的能力。除此之外，Java document metadata API 在处理 DOCX 和 PPTX 文件格式时提供了增强的内存利用率，并支持 DOC 和 DOCX 格式的更多元数据键等等。

以下代码片段演示了如何使用受密码保护的 Microsoft Word 文档:

`// For complete examples and data files, please go to https://github.com/groupdocs-metadata/GroupDocs.Metadata-for-.NET
LoadOptions loadOptions = new LoadOptions("password");
try (DocFormat format = new DocFormat(Common.mapSourceFilePath(protectedFilePath), loadOptions))
{
// Working with the password-protected document
format.cleanMetadata();
format.save(Common.mapDestinationFilePath(protectedFilePath));
}`

同样，接下来是更多的代码片段，分别展示了如何访问受密码保护的 PowerPoint、Excel 和 PDF 文档。

*微软 PowerPoint 演示文稿:*

`https://github.com/groupdocs-metadata/GroupDocs.Metadata-for-.NET
LoadOptions loadOptions = new LoadOptions("password");
try (PptFormat format = new PptFormat(Common.mapSourceFilePath(protectedFilePath), loadOptions))
{
// Working with the password-protected document
format.cleanMetadata();
format.save(Common.mapDestinationFilePath(protectedFilePath));
}`

*微软 Excel 电子表格:*

`LoadOptions loadOptions = new LoadOptions("password");
try (XlsFormat format = new XlsFormat(Common.mapSourceFilePath(protectedFilePath), loadOptions))
{
// Working with the password-protected document
format.cleanMetadata();
format.save(Common.mapDestinationFilePath(protectedFilePath));
}`

*PDF 文档:*

`LoadOptions loadOptions = new LoadOptions("password");
try (PdfFormat format = new PdfFormat(Common.mapSourceFilePath(protectedFilePath), loadOptions))
{
// Working with the password-protected document
format.cleanMetadata();
format.save(Common.mapDestinationFilePath(protectedFilePath));
}`

阅读更多—[http://bit.ly/2LL2tvV](http://bit.ly/2LL2tvV)