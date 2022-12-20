# 使用 Java 电子签名 API 向 PDF 文档添加元数据签名

> 原文：<https://dev.to/groupdocs/add-metadata-signatures-to-pdf-documents-using-the-java-esignature-api-4863>

为您的 PDF 文档添加和搜索元数据签名功能已作为新发布的 GroupDocs 版本的一部分引入。Java 的签名。应用程序开发人员可以通过编程验证加密的二维码文本，将图像导出为 PDF，用像素测量单位在 Microsoft Excel 文档中设置电子签名位置；在 Java 应用程序中对齐 Microsoft Word DOCX、PowerPoint PPTX、PDF 文档和图像文件的数字签名中的文本-[http://bit.ly/2BxcNTd](http://bit.ly/2BxcNTd)

以下代码片段显示了如何向 PDF 文件添加元数据电子签名:

`// setup options with text of signature
PdfMetadataSignOptions signMetadataOptions = new PdfMetadataSignOptions();
// Specify different Metadata Signatures and add them to options sigature collection
// setup Author property
PdfMetadataSignature mdSign_Author = new PdfMetadataSignature("Author", "Mr.Scherlock Holmes");
signMetadataOptions.getMetadataSignatures().add(mdSign_Author);
// setup data of document id
PdfMetadataSignature mdSign_DocId = new PdfMetadataSignature("DocumentId", java.util.UUID.randomUUID().toString());
signMetadataOptions.getMetadataSignatures().add(mdSign_DocId);
// setup data of sign date
PdfMetadataSignature mdSign_Date = new PdfMetadataSignature("SignDate", new Date(), "pdf");
signMetadataOptions.getMetadataSignatures().add(mdSign_Date);`

以下代码片段用于搜索元数据电子签名功能:

`// setup search options
PdfSearchMetadataOptions searchOptions = new PdfSearchMetadataOptions();
// search document
SearchResult result = handler.search("SignedMetadata.pdf", searchOptions);`