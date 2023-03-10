# 用 Java 从 Pdf 中读取/提取文本

> 原文：<https://dev.to/eiceblue/readextract-text-from-pdf-in-java-3cp3>

在本文中，我们将解释如何用 Java 从 Pdf 文件中读取/提取文本。

内容概述:

1.  读取/提取 Pdf 中的所有文本
2.  从 Pdf 页面中的特定矩形区域读取/提取文本
3.  使用 SimpleTextExtractionStrategy 读取/提取文本

**我们需要的 Pdf 库:**

Spire.PDF 为爪哇(**尖顶。Pdf.jar** 下载链接:【https://www.e-iceblue.com/Download/pdf-for-java.html】T2

> ***Spire.PDF for Java info:**Spire.PDF for Java 是一个专业的 Java 组件，使开发人员能够从头开始创建 Pdf 文件或在 Java 应用程序中处理现有的 Pdf 文件，而无需安装 Adobe Acrobat。*

示例 Pdf 文件:
[![alt text](img/1db28d660a062dcacc6f3e996427e51a.png "Example Pdf file")](https://res.cloudinary.com/practicaldev/image/fetch/s--tn_s54sw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mgmqkn921g57254xbrx6.png)

# 样本代码

**导入的名称空间**

```
import com.spire.pdf.*;
import com.spire.pdf.exporting.text.SimpleTextExtractionStrategy;

import java.awt.geom.Rectangle2D;
import java.io.*; 
```

**读取/提取 Pdf 中的所有文本**

```
//Instantiate a PdfDocument object
PdfDocument pdf = new PdfDocument();
//Load the Pdf file
pdf.loadFromFile("Additional.pdf");

StringBuilder sb= new StringBuilder();

//Extract text from every page of the Pdf
for (PdfPageBase page: (Iterable<PdfPageBase>) pdf.getPages()) {
    sb.append(page.extractText(true));
}

try {
    //Write the text into a .txt file 
    FileWriter writer = new FileWriter("ExtractText.txt");
    writer.write(sb.toString());
    writer.flush();
} catch (IOException e) {
    e.printStackTrace();
}

//Close the PdfDocument object
pdf.close(); 
```

输出: [![alt text](img/e02656282e0776dc3cd2d9f68b42fe34.png "Extract all text from Pdf using Spire.PDF for Java")](https://res.cloudinary.com/practicaldev/image/fetch/s--s1rBUhNW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9yk5ewcr1e1dzo88drow.png)

**从 Pdf 页面的特定矩形区域读取/提取文本**

```
//Instantiate a PdfDocument object
PdfDocument pdf = new PdfDocument();
//Load the Pdf file
pdf.loadFromFile("Additional.pdf");

//Get the first page of the Pdf
PdfPageBase page = pdf.getPages().get(0);

//Instantiate a Rectangle2D object 
Rectangle2D rect = new Rectangle2D.Float();
//Set location and size
rect.setFrame( 50, 50, 500, 100);

//Extract text from the given rectangle area in the first page
StringBuilder sb= new StringBuilder();
StringBuilder append = sb.append(page.extractText(rect));

try {
    //Write the text into a .txt file 
    FileWriter writer = new FileWriter("ExtractText.txt");
    writer.write(sb.toString());
    writer.flush();
} catch (IOException e) {
    e.printStackTrace();
}

//Close the PdfDocument object
pdf.close(); 
```

输出: [![alt text](img/b6f4bf2dda091d368fcc72fc6d966070.png "Extract text from a specific rectangle area using Spire.PDF for Java")](https://res.cloudinary.com/practicaldev/image/fetch/s--uy7Z8loG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ydl0b7smpc40dno63d1e.png)

**使用简单文本提取策略读取/提取文本**

```
//Instantiate a PdfDocument object
PdfDocument pdf = new PdfDocument();
//Load the Pdf file
pdf.loadFromFile("Additional.pdf");

//Get the first page of the Pdf
PdfPageBase page = pdf.getPages().get(0);

//Extract text from the first page using SimpleTextExtractionStrategy
SimpleTextExtractionStrategy strategy = new SimpleTextExtractionStrategy();
StringBuilder sb= new StringBuilder();
StringBuilder append = sb.append(page.extractText(strategy));

try {
    //Write the text into a .txt file 
    FileWriter writer = new FileWriter("ExtractText.txt");
    writer.write(sb.toString());
    writer.flush();
} catch (IOException e) {
    e.printStackTrace();
}

//Close the PdfDocument object
pdf.close(); 
```

输出: [![alt text](img/960f172ffc71bb20ca351a1953c4dfbe.png "Extract Text using SimpleTextExtractionStrategy using Spire.PDF for Java")](https://res.cloudinary.com/practicaldev/image/fetch/s--OHBBSzbv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dhvvut9r53y7zvmbdau7.png)