# 认识 JFlex

> 原文：<https://dev.to/vicentemaldonado/meet-jflex-59gd>

[![](img/04286f514863514deabbdfd6deaac599.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--95HPdMFE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/570/1%2AS7svjAbwFuRI5_C5h7gsRA.jpeg)

JFlex 是一个用于 Java 的扫描仪生成器。一个[扫描仪生成器](https://en.wikipedia.org/wiki/Comparison_of_parser_generators#Regular_languages)会为你生成一个[扫描仪(又名 lexer)](https://en.wikipedia.org/wiki/Lexical_analysis) ，而不是你必须自己写一个。JFlex 是模仿 [(f)](https://en.wikipedia.org/wiki/Flex_(lexical_analyser_generator)) [lex](https://en.wikipedia.org/wiki/Lex_(software)) 的，只是它是用 Java 编写的，并且生成 Java lexers，不像两个旧的工具。

什么是 JFlex 工作流？

*   创建 JFlex 源文件(*。flex)
*   使用 JFlex 命令行工具将文件编译成 Java 文件
*   使用 javac 编译 Java 文件
*   调用*。类别文件

瞧，你有一个工作的扫描仪/lexer。您可以将它作为一个独立的工具使用，也可以与其他程序结合使用——像 Yacc/Bison 这样的工具通常需要一个扫描器来输入数据。

JFlex 源文件由三部分组成:

*   [usercode](https://www.jflex.de/manual.html#ExampleUserCode) ,
*   [选项和声明](https://www.jflex.de/manual.html#ExampleOptions)和
*   [词汇规则](https://www.jflex.de/manual.html#ExampleLexRules)。

用双百分号(%%)分隔。这里有一个简单的例子:

```
import java.io.\*;

%% 
```

第一部分到此为止。奇怪的是，如果您想将代码添加到生成的 Java 类中，您必须将代码包含在 JFlex 文件的中间部分(选项和声明)。这并没有什么神奇之处:JFlex 基于一个模板创建了 lexer，您在第一部分中放入的代码并没有作为生成的类的一部分结束——这就是为什么您将导入语句放在这里(您也可以大胆地将完整的 Java 类放在那里，但这并不是一个好主意)。

另一方面，放在 JFlex 文件中间部分的代码最终会成为 lexer 的一部分。让我们将 main 方法添加到类中，并使其自包含:

```
%{

public static void main(String[] args) throws IOException
{
 InputStreamReader reader =
 new InputStreamReader(System.in);

Lexer lexer = new Lexer(reader);

 System.out.println("Start lexing");

 while (true)
 {
 System.out.println(lexer.yylex());
 }
}

%} 
```

JFlex 生成的 lexer 需要用 Java Reader 初始化。在这种情况下，我们将接受来自 System.in 的输入。stdio。JFlex 用一个名为 Yylex()的函数生成一个名为 yylex 的类。让我们改变一下(我们仍然在 JFlex 文件的中间部分):

```
%class Lexer
%type String

%% 
```

这将使 JFlex 将类名改为 Lexer，yylex()将返回一个 Java 字符串而不是 YY token——我们不会费心创建这个类。

这里的计划是让 yylex()返回我们在键盘上输入的任何字符——这就是为什么我们将它的%type 指定为 String。然后我们就用一个无限循环(while (true))来接受字符并立即打印出来。

让我们结束第三部分(词汇规则):

```
[^] { return yytext(); } 
```

[^]将匹配任何字符。yytext()将以字符串形式返回字符，并且{ return...}是 yylex()将返回的值，所以我们完成了。

下面是完整的文件:

```
import java.io.\*;

%%

%{

public static void main(String[] args) throws IOException
{
 InputStreamReader reader =
 new InputStreamReader(System.in);

Lexer lexer = new Lexer(reader);

 System.out.println("Start lexing");

 while (true)
 {
 System.out.println(lexer.yylex());
 }
}

%}

%class Lexer
%type String

%%

[^] { return yytext(); } 
```

您需要编译它(注意输出中的 jflex 错误消息):

```
[johnny@test example1]$ jflex Lexer.flex 
```

编译生成的 Java 文件:

```
[johnny@test example1]$ javac Lexer.java 
```

并运行它:

```
[johnny@test example1]$ java Lexer 
```

下面是一个终端会话的例子:

```
Start lexing
123
1
2
3

abc 
a
b
c

^C[johnny@test example1]$ 
```

使用 ctrl-c 停止程序。当然，这不是很令人兴奋，你不必使用 570 loc Java 文件(是的，这就是生成的 lexer 的长度)来回显字符。

可以从 [Github](https://github.com/Wurdlack/Medium/blob/master/meet_jflex/Lexer.flex) 下载源代码。