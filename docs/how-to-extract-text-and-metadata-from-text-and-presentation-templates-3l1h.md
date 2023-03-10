# 如何从文本和演示模板中提取文本和元数据

> 原文：<https://dev.to/groupdocs/how-to-extract-text-and-metadata-from-text-and-presentation-templates-3l1h>

使用 GroupDocs 从 Java 平台上的许多不同的文本和表示模板中提取文本和元数据。Java API 解析器。支持以下模板格式:

*   dotx (Template)
*   启用宏的模板
*   OpenDocument 文本模板
*   potx(模板)
*   启用宏的模板
*   ppsm(启用宏的幻灯片)
*   启用宏的演示文稿

下面的代码示例演示了如何从模板中提取文本和元数据。

`// Extracting Text
void extractText(String fileName) {
// Extract a text from the file
String text = Extractor.DEFAULT.extractText(fileName);
// Print an extracted text
System.out.println(text);
}
// Extracting Metadata
void extractMetadata(String fileName) {
// Extract metadata from the file
MetadataCollection metadata = Extractor.DEFAULT.extractMetadata(fileName);
// Print extracted metadata
for (String key : metadata.getKeys()) {
// Print a metadata key
System.out.print(key);
System.out.print(": ");
// Print a metadata value
System.out.println(metadata.get_Item(key));
}
}`

除此之外，解析 API 还支持从 PDF 文档中检索表格，并允许识别安全 Office Open XML 文档的媒体类型-[http://bit.ly/2CCy7bX](http://bit.ly/2CCy7bX)