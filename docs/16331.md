# 如何使用 C#操作您的安全 PDF、Words、Excel、PowerPoint 文档？NET 元数据 API

> 原文：<https://dev.to/groupdocs/how-to-manipulate-your-secure-pdf-words-excel-powerpoint-documents-using-c-net-metadata-api-2apo>

元数据被认为是文件中“关于数据的数据”,程序员操纵它来存储以及从特定文档中提取不同种类的重要信息，这些信息可能与它的各种特征有关，例如用户权限。考虑到他可能需要经常使用在线文件传输服务的次数，文件安全性是任何专业人员主要关心的问题之一。因此，我们一直在展望软件应用程序和 API，它们在我们可以使用它们操纵的数据以及它们可以让我们管理的文件类型方面是可靠的。

根据任何组织甚至个人用户的业务需求，可以通过编程方式处理一种或多种类型的文档，以获取有价值的信息，如。NET 平台。再加上使用加密或密码保护文档的要求，能够满足这两个先决条件的应用程序非常少。GroupDocs。的元数据。NET API 就是这样一个选项，它涵盖了前面提到的两个需求并提供了更多。应用程序开发人员可以使用它从不同类型的受保护文档(如 Microsoft Word 和 PDF 文件、Excel 电子表格或 PowerPoint 演示文稿)中读取、写入、分析和擦除元数据。

另外这个。NET document metadata API 允许您在应用程序中加载或保存文字处理和幻灯片文档时更好地利用内存，并能够更新 DOC 和 DOCX 文件格式的元数据密钥。了解更多—
[http://bit.ly/2QESWr1](http://bit.ly/2QESWr1)

以下代码片段显示了如何使用受密码保护的 PDF 文档的元数据:

`LoadOptions loadOptions = new LoadOptions("password");
using (PdfFormat format = new PdfFormat(Common.MapSourceFilePath(protectedFilePath), loadOptions))
{
// Working with the password-protected document
format.CleanMetadata();
format.Save(Common.MapDestinationFilePath(protectedFilePath));
}`

这个。NET API 支持使用较少内存消耗的 Word 格式。请注意，DocFormat 类实现了 IDisposable 接口，当您使用完它的实例时，有必要调用 Dispose()方法。

检查下列代码片段以在使用 Word 文档时使用较少的内存。

`using (DocFormat format = new DocFormat(@"d:\input.docx"))
{
// Working with metadata
}`

如果您从流中加载一个 Word 文档，当不再需要该文件时，由您决定是否关闭该流。

`using (Stream stream = File.Open(@"d:\input.doc", FileMode.Open, FileAccess.ReadWrite))
{
using (DocFormat format = new DocFormat(stream))
{
// Working with metadata
}
// The stream is still open here
}`