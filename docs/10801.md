# 使用 JFlex 进行字数统计

> 原文：<https://dev.to/vicentemaldonado/use-jflex-to-count-words-5ci4>

[![](img/24e3f103456b4910580b673bf0209b85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fmxN-aDB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/612/1%2An6Ziy9s7A21I1JyCPSX4Jg.jpeg)

在[之前的故事](https://dev.to/wurdlack/meet-jflex-20de-temp-slug-2207783)中，我们见到了 JFlex，一个用 Java 生成词法分析器的工具。这个 lexer 例子是人为设计的，很普通，也不是很有用，所以让我们用一个(有点)更有用的例子来展示 JFlex 的用处:我们将计算用户输入的单词、行和字符。

JFlex 文件的第一部分与第一个例子中的相同:

```
import java.io.\*;

%% 
```

我们只是导入所有的 java.io 类。首先，我们需要一种方法来存储我们的单词、行和字符数:

```
%{

public int chars = 0;
public int words = 0;
public int lines = 0; 
```

字符、单词和行将成为生成的类的公共成员，代码的其余部分可以访问它们。主要的方法与第一个例子非常相似:

```
public static void main(String[] args) throws IOException
{
 InputStreamReader reader =
 new InputStreamReader(System.in);

Lexer lexer = new Lexer(reader);

lexer.yylex();

 System.out.format(
 "Chars: %d\nWords: %d\nLines: %d\n",
 lexer.chars, lexer.words, lexer.lines);
}

%} 
```

但是有两个不同之处:

*   没有无限循环 lexer 将从 System.in 中读取数据，直到我们中断它(我认为在 Linux 上是 Ctrl-d，在 Windows 上是 Ctrl-Z)。这允许用户在终端中输入几行文本。
*   我们不直接使用 yylex()。

在下一部分:

```
%class Lexer
%type Integer

%% 
```

生成的 Java 类将被命名为 Lexer，yylex()将返回一个 Java 整数值。这只是因为 yylex()需要返回一些东西，它默认返回一个 Yylex 类型的对象——javac 会抱怨 Yylex 类型不存在，因为它不存在(如果不是你自己创建的)。

最后，在词法规则部分:

```
[a-zA-Z]+ { words++; chars += yytext().length(); }
\n { chars++; lines++; }
. { chars++; } 
```

如果您键入一个单词，被[a-zA-Z]+ regex 识别，单词计数将增加，字符计数将增加输入的单词长度。如果你按回车键。\n，字符计数将增加。如果你输入一个像*或&这样的随机字符，字符数就会增加。

这允许我们打印出字符、单词和行的最终计数(回到 main):

```
System.out.format(
 "Chars: %d\nWords: %d\nLines: %d\n",
 lexer.chars, lexer.words, lexer.lines); 
```

下面是完整的文件:

```
import java.io.\*;

%%

%{

public int chars = 0;
public int words = 0;
public int lines = 0;

public static void main(String[] args) throws IOException
{
 InputStreamReader reader =
 new InputStreamReader(System.in);

Lexer lexer = new Lexer(reader);

lexer.yylex();

 System.out.format(
 "Chars: %d\nWords: %d\nLines: %d\n",
 lexer.chars, lexer.words, lexer.lines);
}

%}

%class Lexer
%type Integer

%%

[a-zA-Z]+ { words++; chars += yytext().length(); }
\n { chars++; lines++; }
. { chars++; } 
```

和前面的例子一样，您需要编译 JFlex 文件和生成的 Java 文件:

```
[johnny@test example1]$ jflex Lexer.flex
[johnny@test example1]$ javac Lexer.java
[johnny@test example1]$ java Lexer 
```

下面是一个简单的演示终端会话:

```
The quick brown fox
jumps over the lazy dog.
Chars: 45
Words: 9
Lines: 2 
```

你可以从 [Github](https://github.com/Wurdlack/Medium/blob/master/word_count/Lexer.flex) 下载完整代码。