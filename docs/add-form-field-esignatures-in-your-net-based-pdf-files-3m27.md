# 在中添加表单域电子签名。基于. NET 的 PDF 文件

> 原文：<https://dev.to/groupdocs/add-form-field-esignatures-in-your-net-based-pdf-files-3m27>

GroupDocs。签名为。NET 是一个设计 API 的文档。NET 平台，支持跨不同类型的 web、桌面和移动应用程序的大量非常有用的高级功能。以下代码片段演示了应用程序开发人员如何在上的 PDF 文档中插入复选框表单字段签名。NET 平台。

*//实例化复选框表单域签名
formfield signature checkbox signature = new pdfcheckboxformfield signature(" field checkbox "，true)；
//基于复选框表单字段签名实例化选项
PdfFormFieldSignOptions checkbox options = new PdfFormFieldSignOptions(checkbox signature)
{
horizontal alignment = horizontal alignment。左，
vertical alignment = vertical alignment。Top，
Margin =新填充(120，20，0，0)，
Height = 10，
Width = 10
}；
//签署文档
字符串 signedPath = handler。Sign("02_pages.pdf "，checkboxOptions，
new save options { output type = output type。String，output filename = " Pdf _ form fields " })；*