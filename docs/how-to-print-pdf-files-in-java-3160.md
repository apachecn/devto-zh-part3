# 如何用 Java 打印 PDF 文件

> 原文：<https://dev.to/eiceblue/how-to-print-pdf-files-in-java-3160>

用 Java 打印 PDF 文件是一个常见的需求，我们总是需要打印 PDF 文件。在这篇文章中，我将从以下三个方面举例说明如何使用 Java Spire.PDF 库在 Java 应用程序中打印 PDF:

*   使用默认打印机静默打印 PDF 文档
*   使用打印对话框打印 PDF 文档
*   使用自定义页面尺寸打印 PDF 文档

开始前

从 Maven 资源库安装 Java 版 Spire.PDF。下载内容包含两个 jar，应该在 Java 项目中引用它们。

在不显示打印对话框的情况下将 PDF 文档打印到默认打印机，我们还可以自定义一些打印设置，例如删除默认打印边距，设置份数等。

```
import com.spire.pdf.*;
import java.awt.print.*;

public class Print {
    public static void main(String[] args) {
        //load the sample document
        PdfDocument pdf = new PdfDocument();
        pdf.loadFromFile("Sample.pdf");

        PrinterJob loPrinterJob = PrinterJob.getPrinterJob();
        PageFormat loPageFormat  = loPrinterJob.defaultPage();
        Paper loPaper = loPageFormat.getPaper();

        //remove the default printing margins
        loPaper.setImageableArea(0,0,loPageFormat.getWidth(),loPageFormat.getHeight());

        //set the number of copies
        loPrinterJob.setCopies(2);

        loPageFormat.setPaper(loPaper);
        loPrinterJob.setPrintable(pdf,loPageFormat);
        try {
            loPrinterJob.print();
        } catch (PrinterException e) {
            e.printStackTrace();
        }
    }
} 
```

用打印对话框打印 PDF。

```
import com.spire.pdf.*;
import java.awt.print.*;

public class Print {
    public static void main(String[] args) {
        //load the sample document
        PdfDocument pdf = new PdfDocument();
        pdf.loadFromFile("Sample.pdf");

        PrinterJob loPrinterJob = PrinterJob.getPrinterJob();
        PageFormat loPageFormat  = loPrinterJob.defaultPage();
        Paper loPaper = loPageFormat.getPaper();

        //remove the default printing margins
        loPaper.setImageableArea(0,0,loPageFormat.getWidth(),loPageFormat.getHeight());

        loPageFormat.setPaper(loPaper);
        loPrinterJob.setPrintable(pdf,loPageFormat);

        //display the print dialog
        if (loPrinterJob.printDialog()) {
            try {
                loPrinterJob.print();
            } catch (PrinterException e) {
                e.printStackTrace();
            }
        }
    }
} 
```

使用自定义页面尺寸打印 PDF 文档。

```
import com.spire.pdf.*;
import java.awt.print.*;

public class Print {

    public static void main(String[] args) {

        //load the sample document
        PdfDocument pdf = new PdfDocument();
        pdf.loadFromFile("Sample.pdf");

        PrinterJob loPrinterJob = PrinterJob.getPrinterJob();
        PageFormat loPageFormat  = loPrinterJob.defaultPage();

       //set the print page size
        Paper loPaper = loPageFormat.getPaper();
        loPaper.setSize(500,600);
        loPageFormat.setPaper(loPaper);
                loPrinterJob.setPrintable(pdf,loPageFormat);

        try {
            loPrinterJob.print();
        } catch (PrinterException e) {
            e.printStackTrace();
        } 
    }
} 
```

结论

在 Java 应用程序中打印 PDF 文件非常简单。这只是我在打印 PDF 的过程中使用的一个简单的例子。它还支持对 PDF 文件的其他操作，如将 PDF 转换为图像，将数字签名添加到 PDF 文件中。你可以自己探索更多。