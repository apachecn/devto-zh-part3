# Java 查找和替换 Word 文档中的文本

> 原文：<https://dev.to/eiceblue/java-find-and-replace-text-on-word-document-1cbk>

在 Microsoft Word 中，我们可以很容易地找到特殊的文本字符串并用新的文本字符串替换它们。本文将提到在 word 上查找和替换功能的各种方法

使用 Java API Spire 编写文档。如下独立 Doc(不使用任何第三方代码)。

*   在 Word 中查找文本字符串并用新的文本字符串替换它
*   用正则表达式查找和替换文本
*   用 HTML 查找和替换文本

在 Word 中查找文本字符串并用新的文本字符串替换它。所有搜索到的结果将被一次性检测和替换。

```
import com.spire.doc.*;
import com.spire.doc.documents.TextSelection;
import java.awt.*;

public class Word {
           public static void main(String[] args) {

               String input = "replaceWithText.docx";
               String output = "out/replaceWithText.docx";
               //load Word document
               Document document = new Document();
               document.loadFromFile(input, FileFormat.Docx);

               //replace the text
               document.replace("Word", "NewWord", false, true);

               //highlight the replaced text

               TextSelection[] textSelections = document.findAllString("NewWord", false, true);

               //set highlight
               for (TextSelection selection : textSelections) {
                   selection.getAsOneRange().getCharacterFormat().setHighlightColor(Color.YELLOW);
               }

               //save the document
               document.saveToFile(output,FileFormat.Docx);
           }
} 
```

在 Word 文档中查找与特定正则表达式匹配的单词，并用新字符串替换匹配的单词。以下示例将向您展示如何替换正则表达式

对于以“#”开头的单词。

```
import com.spire.doc.*;
import java.util.regex.Pattern;
import com.spire.doc.documents.TextSelection;
import java.awt.*;

public class Word {
           public static void main(String[] args) {
               String input = "replaceTextByRegex.docx";
               String output = "out/replaceTextByRegex.docx";
               //load Word document
               Document document = new Document();
               document.loadFromFile(input, FileFormat.Docx);

               //create a regex, match the text that starts with "#"
               String regEx="\\#\\w+\\b";
               Pattern pattern= Pattern.compile(regEx);

               //replace the text by regex
               document.replace(pattern,"JavaSpire.Doc");

               //highlight the replaced text

               TextSelection[] textSelections = document.findAllString("JavaSpire.Doc", false, true);

               //set highlight
               for (TextSelection selection : textSelections) {
                   selection.getAsOneRange().getCharacterFormat().setHighlightColor(Color.YELLOW);
               }

               //save the document
               document.saveToFile(output,FileFormat.Docx);
           }
} 
```

用 HTML 查找和替换 word 中的文本。

```
import com.spire.doc.*;
import com.spire.doc.documents.*;
import com.spire.doc.fields.TextRange;;
import java.io.*;

public class Word{
           public static void main(String[] args) throws Exception{
               String input = "replaceWithHtml.docx";
               String inputHtml = "replaceWithHtml.txt";
               String output = "out/replaceWithHtml.docx";
               //load Word document
               Document document = new Document();
               document.loadFromFile(input, FileFormat.Docx);

               //find a paragraph and replace its content with html
               TextSelection textSelection = document.findString("[#placeholder]", false, true);
               TextRange textrange =textSelection.getAsOneRange();
               Paragraph par= textrange.getOwnerParagraph();
               String htmlText = readTextFromFile(inputHtml);
               par.appendHTML(htmlText);
               par.getChildObjects().removeAt(0);
               //save the document
               document.saveToFile(output,FileFormat.Docx);
           }

    public static String readTextFromFile(String fileName) throws IOException{
        StringBuffer sb = new StringBuffer();
        BufferedReader br = new BufferedReader(new FileReader(fileName));
        String content = null;
        while ((content = br.readLine()) != null) {
            sb.append(content);
        }
        return sb.toString();
    }
} 
```

结论

使用 Spire 对我们来说很容易。Doc 在 Java 应用程序中用新的文本字符串和 HTML 查找和替换 word 文档中的文本。感谢您的阅读，希望对您有所帮助。