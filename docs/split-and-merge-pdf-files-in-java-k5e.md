# 在 Java 中分割和合并 PDF 文件

> 原文：<https://dev.to/eiceblue/split-and-merge-pdf-files-in-java-k5e>

随着 PDF 文件格式在商业领域越来越受欢迎，办公室工作人员经常接触到 PDF 文档，并且可能也需要处理 PDF。例如，将包含多张发票的 PDF 分割成单独的文件，或将多个报告合并成一个 PDF。

这篇文章将介绍我们如何使用 Java Spire.PDF 来分割一个大的 PDF 和合并多个 PDF。下面几节中有三段代码从以下三个方面说明了拆分和合并。

*   将 PDF 分割成多个单页 PDF
*   按页面范围将 PDF 分割成几个文件
*   将多个 pdf 合并到一个文档中

在编译下面的代码片段之前，[下载 Java 版 Spire.PDF](https://www.e-iceblue.com/Download/pdf-for-java.html)并导入 Spire。jar 和 Spire。Common.jar 到您的项目中。

## 将 PDF 分割成单页 PDF

Spire 提供了一个非常简单的方法——分割，将一个多页的 PDF 文档分割成多个单页的 PDF 文档。只需将源 PDF 文件加载到 PdfDocument 对象中，然后调用 split 方法进行分割。

```
//specify input file path and output directory
String inputFile= "C:/Users/Administrator/Desktop/test.pdf";
String outputDirectory = "C:/Users/Administrator/Desktop/Output/";

//create a PdfDocument instance
PdfDocument doc = new PdfDocument();

//load a PDF file
doc.loadFromFile(inputFile);

//split PDF to single-page PDFs
doc.split(outputDirectory + "output-{0}.pdf",1); 
```

## 按页面范围分割 PDF

如果您希望 PDF 按照所选页码进行拆分，例如，第一页变成一个文档，其余页面变成另一个文档，那么您需要创建两个新的 PDF 文档，并将源文档中的所选页面插入到新文档中。下面的代码片段演示了同样的情况。

```
//specify input file path and output directory
String inputFile= "C:/Users/Administrator/Desktop/test.pdf";
String outputDirectory = "C:/Users/Administrator/Desktop/Output/";

//create a PdfDocument instance
PdfDocument sourceDoc = new PdfDocument();

//load a source PDF file
sourceDoc.loadFromFile(inputFile);

//create two extra PdfDocument objects
PdfDocument newDoc_1 = new PdfDocument();
PdfDocument newDoc_2 = new PdfDocument();

//insert the first page from source file to the new PDF document
newDoc_1.insertPage(sourceDoc, 0);

//insert the rest pages from source file to the second new PDF document
newDoc_2.insertPageRange(sourceDoc,1,sourceDoc.getPages().getCount()-1);

//save the new PDF documents
newDoc_1.saveToFile(outputDirectory + "output-1.pdf");
newDoc_2.saveToFile(outputDirectory + "output-2.pdf"); 
```

## 合并 pdf

为了完成这项任务，Spire 提供了另一个简单易用的方法 mergeFiles。只需获取输入文件路径并将其存储在一个字符串数组中，然后将其作为一个参数传递给 mergeFiles 方法，然后将返回的对象保存为一个新的 PDF 文件。

```
//get the paths of the files that you want to merge
String[] filePaths = new String[]{
        "C:/Users/Administrator/Desktop/Source/input-1.pdf",
        "C:/Users/Administrator/Desktop/Source/input-2.pdf",
        "C:/Users/Administrator/Desktop/Source/input-3.pdf"
};

//specify output directory
String outputDirectory = "C:/Users/Administrator/Desktop/Output/";

//merge PDF files using mergeFiles method
PdfDocumentBase newDoc = PdfDocument.mergeFiles(filePaths);

//save the new PDF document
newDoc.save(outputDirectory + "Merged.pdf"); 
```