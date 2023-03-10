# 如何在 Java 中将 word 转换成 PDF

> 原文：<https://dev.to/eiceblue/how-to-convert-word-to-pdf-in-java-3bmo>

将 word 转换为 PDF 是处理 word 文档时最常见的要求。借助自由尖塔。在 Java 应用程序中，从 word 到 PDF 的转换变得非常简单和容易。许多 word 元素可以支持转换为 PDF，如超链接，表格，页眉和页脚，书签，隐藏的字体和段落等。我将从以下两个方面介绍如何将 word 转换成 PDF:

将 word 转换为 PDF 的一体化解决方案
通过定制选项将 Word 转换为 PDF

首先，查看示例 Microsoft word 文档，如下:
[![Sample word](img/8fe557809f696093f276187f71f2ab07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E9nZNP8s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zqujdljvb3f3ts0ovp7k.png)

使用默认选项将 word 转换为 PDF 非常简单。它只需要三行代码。

```
import com.spire.doc.*;
public class WordtoPDF {
    public static void main(String[] args) {
        Document document = new Document();
        document.loadFromFile("Sample.docx");

        document.saveToFile("out/result.pdf", FileFormat.PDF);
    }
} 
```

调试完成后，我们会得到如下的结果 PDF 页面:
[![word to PDF](img/6b57793c66da777e1cd75fa84d5b218b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HQI1fkg_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0mq6wt3n9o89b7kpeg3s.png)

从上面的结果 PDF 页面中，我们发现隐藏的文本和书签没有显示在结果 PDF 页面上。如果你想显示所有这些单词元素，Spire。Java 的文档都支持它。现在来看看通过自定义选项将 word 转换成 PDF 的代码。

```
import com.spire.doc.*;
import com.spire.doc.documents.rendering.*;
import java.awt.*;

public class WordtoPDF {
    public static void main(String[] args) {

        Document document = new Document();
        document.loadFromFile("Sample.docx");

        ToPdfParameterList toPdf = new ToPdfParameterList();

        //embedded all fonts
        toPdf.isEmbeddedAllFonts(true);

        //convert hidden fonts
        toPdf.isHidden(true);

        //disable the hyperlinks
        toPdf.setDisableLink(true);

        //keep bookmarks
        toPdf.setCreateWordBookmarks(true);
        toPdf.setWordBookmarksTitle("Bookmark");
        toPdf.setWordBookmarksColor(Color.GRAY);

        //the event of BookmarkLayout occurs when drawing a bookmark
        document.BookmarkLayout = new BookmarkLevelHandler() {
            @Override
            public void invoke(Object sender, BookmarkLevelEventArgs args) {
                document_BookmarkLayout(sender, args);
            }
        };

        //save the document to a PDF file.
        document.saveToFile("out/Result2.pdf", toPdf);
    }
    private static void document_BookmarkLayout(Object sender, BookmarkLevelEventArgs args) {

        if (args.getBookmarkLevel().getLevel() == 2) {
            args.getBookmarkLevel().setColor(Color.RED);
            args.getBookmarkLevel().setStyle(BookmarkTextStyle.Bold);
        } else if (args.getBookmarkLevel().getLevel() == 3) {
            args.getBookmarkLevel().setColor(Color.GRAY);
            args.getBookmarkLevel().setStyle(BookmarkTextStyle.Italic);
        } else {
            args.getBookmarkLevel().setColor(Color.GREEN);
            args.getBookmarkLevel().setStyle(BookmarkTextStyle.Regular);
        }
    }
} 
```

结果 PDF 页面嵌入字体，隐藏文本和书签。

[![word to PDF 2](img/e478956abd6ce65c20a849aca6eaea16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wG8mm4Un--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pp5sfang4vsgfrs91mjc.png)

结论

对我们来说，使用免费的 Spire 是相当容易的。Doc for Java 用 Java 把 word 转换成 PDF。感谢您的阅读，希望对您有所帮助。