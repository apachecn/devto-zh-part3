# 如何使用从演示模板中提取元数据？NET 解析 API

> 原文：<https://dev.to/groupdocs/how-to-extract-metadata-from-presentation-templates-using-net-parsing-api-31dh>

当涉及到在应用程序中操作文档时，开发选项是无穷无尽的。全球各地的组织，无论其所处的环境如何，都会定期将大量创新功能整合到他们的每个业务场景中，以提高工作效率。从多格式文档中提取不同类型的信息就是这样一个必要条件。然而，一个主要问题是提取数据的准确性或有效性，不是所有的软件应用程序都为开发人员提供高度准确的数据提取功能。

因此，当您在寻找能够从许多不同类型的知名文件格式中精确提取原始和格式化文本以及元数据的应用程序时。NET 平台，GroupDocs。的分析器。NET 必须考虑。除了这个文档文本提取 API 提供的基本数据提取功能之外，应用程序开发人员还可以在最新 API 版本的
帮助下，使用它从各种文本和演示模板中提取文本和元数据。另一个重要特性是能够以编程方式从。网络应用。在使用该功能时，您可以手动创建表格边界，或者让 API 在自动模式下识别布局。

除此之外，您还可以使用检测受密码保护的 Office OpenXML 文档的媒体类型和批处理文档的功能—
[http://bit.ly/2QuFPsr](http://bit.ly/2QuFPsr)

下面的代码示例展示了如何从模板中提取文本和元数据:
`// Extracting Text
void ExtractText(string fileName)
{
// Extract a text from the file
var text = Extractor.Default.ExtractText(fileName);
// Print an extracted text
Console.WriteLine(text);
}
// Extracting Metadata
void ExtractMetadata(string fileName)
{
// Extract metadata from the file
var metadata = Extractor.Default.ExtractMetadata(fileName);
// Print extracted metadata
foreach (var m in metadata)
{
// Print a metadata key
Console.Write(m.Key);
Console.Write(": ");
// Print a metadata value
Console.WriteLine(m.Value);
}
}`

以下代码示例显示了如何在受密码保护的 Office OpenXML 文档中检测媒体类型:

`// Create load options
LoadOptions loadOptions = new LoadOptions();
// Set a password
loadOptions.Password = "password";
// Get a default composite media type detector
var detector = CompositeMediaTypeDetector.Default;
// Create a stream to detect media type by content (not file extension)
using (var stream = File.OpenRead(Common.GetFilePath(fileName)))
{
// Detect a media type
var mediaType = detector.Detect(stream, loadOptions);
// Print a detected media type
Console.WriteLine(mediaType);
}`