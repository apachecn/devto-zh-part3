# 使用 C#在多线程环境下编辑文档。NET API

> 原文：<https://dev.to/groupdocs/editing-documents-in-multi-threaded-environment-using-c-net-api-2b73>

当谈到一般的文档操作时，在不同的开发平台上以编程方式编辑不同类型的文档几乎是必然的。最基本的要求是修改文件内容，然后将文件重新转换为原始格式，甚至是不同的格式。除此之外，还有一些额外的好处，比如加载文档的多个来源，保护结果文档等等。

因此，一个提供文档编辑特性的 API 加上我们想要编辑的文档就是我们开始所需要的。如果。NET 平台是你的强项，可以试试 GroupDocs。的编辑。NET API，这是一个功能丰富的文件编辑器，帮助您开发简单、可扩展的 C#和 ASP.NET 应用程序，这些应用程序与不同的基于 HTML 的编辑器集成，以修改您的 Microsoft Word、Excel、OpenDocument、Text、HTML 和其他类型的文档。

这个的最新版本。NET API 支持在 HTML 编辑器中使用一组精炼的列表和段落。此外，您还可以访问更稳定的多线程环境，从而进一步增强您的文档编辑体验。

下面的代码片段显示了如何获得 HTML 文档及其嵌入的资源:
`public static void GetHTMLContentsWithEmbeddedResources()
{
// Obtain document stream
Stream sourceStream = File.Open(Path.Combine(Common.sourcePath, Common.sourceFile), FileMode.Open, FileAccess.Read);
using (InputHtmlDocument htmlDoc = EditorHandler.ToHtml(sourceStream))
{
// Obtain HTML document with embedded resources
string cssContent = htmlDoc.GetEmbeddedHtml();
Console.WriteLine(cssContent);
}
// close stream object to release file for other methods.
sourceStream.Close();
}`

更多的帮助和示例代码可以在 docs resource-[http://bit.ly/2GBKvvk](http://bit.ly/2GBKvvk)获得

同样，如果你想把 HTML DOM 转换成一个文档，请参考下面的代码片段。它专门从字符串内容中获取 HTML DOM 并将其保存到文档中:

`public static void GetHTMLDOMContentsToDocument()
{
// Obtain document stream
Stream sourceStream = File.Open(Path.Combine(Common.sourcePath, Common.sourceFile), FileMode.Open, FileAccess.Read);
using (InputHtmlDocument htmlDoc = EditorHandler.ToHtml(sourceStream))
{
// Obtain HTML document content
string htmlContent = htmlDoc.GetContent();
using (OutputHtmlDocument editedHtmlDoc = OutputHtmlDocument.FromMarkup(htmlContent, Path.Combine(Common.sourcePath, Common.resultResourcesFolder)))
{
using (System.IO.FileStream outputStream = System.IO.File.Create(Path.Combine(Common.resultPath, Common.resultFile)))
{
WordsSaveOptions saveOptions = new WordsSaveOptions();
EditorHandler.ToDocument(editedHtmlDoc, outputStream, saveOptions);
}
}
}
// close stream object to release file for other methods.
sourceStream.Close();
}`

今天就免费试用—[http://bit.ly/2DZY99S](http://bit.ly/2DZY99S)

关注 YouTube 上的群文档–[http://bit.ly/2UPmVPr](http://bit.ly/2UPmVPr)