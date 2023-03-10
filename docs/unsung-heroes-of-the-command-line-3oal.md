# 指挥线上的无名英雄

> 原文：<https://dev.to/hoelzro/unsung-heroes-of-the-command-line-3oal>

*最初发表于[https://hoelz.ro/blog/unsung-heroes-of-the-command-line](https://hoelz.ro/blog/unsung-heroes-of-the-command-line)T3】*

由于命令行是我与计算机交互的主要方式，我经常会采取措施来优化它的使用。大多数命令行用户都知道一些基本知识:`ls`、`cd`、`grep`等等——这些都是使用命令行的工具性构件。然而，通常有更多的专业计划可以真正节省您的时间，我想分享一些这类专业计划的显著例子，它们在过去的一两年里使我的生活变得更加轻松。如果你不知道它们，希望你会发现它们很有用；如果你*真的*认识他们，希望这只是表明我们对自己的工具都有很好的品味。=)

# 魔咒

在过去的几年里，我发现自己越来越多地在网上处理数据。调用各种 web 服务或从 URL 获取内容已经成为开发人员的重要组成部分。当需要简单地获取数据时，我使用了`curl`。然而，如果我不得不做一些切片和切块，我期待我的好朋友`mojo`。

`mojo`是与 [Mojolicious](http://mojolicious.org/) 项目一起提供的命令行实用程序——它是一种针对 Perl 程序员的 web 工具包。然而，使用`mojo`并不需要精通 Perl。`mojo get`允许你从一个 URL 获取内容，通过 CSS3 选择器选择特定的元素，然后使用各种方法转换结果。例如，假设我想获得页面上每个`img`元素的`src`属性。这就是我如何调用`mojo get`来完成工作的方法:

```
 $ mojo get http://example.com img attr src 
```

`img`部分当然是元素选择器；它后面的参数是一个方法名(可以使用 [Mojo::DOM](https://metacpan.org/pod/Mojo::DOM) 中的任何方法)加上该方法的任何参数。我发现`mojo get`在抓取文档并从中提取一些内容以供命令行进一步使用方面非常有用。

# jq

随着在线数据趋势的发展，我发现自己也经常使用 JSON。您可以使用 [jq](https://stedolan.github.io/jq/) 来代替编写脚本从 JSON 中提取数据。这是一个方便的程序，将自己描述为“JSON 数据的 sed”，我觉得它符合这个想法。只需调用`.`操作符(选择当前对象)就会打印出一大块 JSON:

```
 $ cat chunk-of.json | jq . 
```

您可以通过在`.`操作符:
之后添加一个键来选择文档中的特定键

```
 $ cat chunk-of.json | jq .result 
```

选择器语法非常容易理解和记忆——每次使用它时，我都能可靠地记住如何做基本操作。在其他情况下，文档随时可供参考！

# uniprops & unichars

我试图编写支持 Unicode 的软件，这通常意味着检查`Letter`属性，而不是简单地寻找`a-z`和`A-Z`。为了帮助我，我使用了来自[Unicode::tustle](https://metacpan.org/pod/Unicode::Tussle)CPAN 发行版的两个脚本:`uniprops`和`unichars`。

要使用`uniprops`，您可以给出一个字符或代码点，它会告诉您该字符显示的所有 Unicode 属性。比如:

```
 $ uniprops а
    U+0430 ‹а› \N{CYRILLIC SMALL LETTER A}
        \w \pL \p{LC} \p{L_} \p{L&} \p{Ll}
        All Alnum X_POSIX_Alnum Alpha X_POSIX_Alpha Alphabetic Any Assigned
           InCyrillic Cyrillic Is_Cyrillic ID_Continue Is_IDC Cased Cased_Letter LC
           Changes_When_Casemapped CWCM Changes_When_Titlecased CWT
           Changes_When_Uppercased CWU Cyrl Ll L Gr_Base Grapheme_Base Graph
           X_POSIX_Graph GrBase IDC ID_Start IDS Letter L_ Lowercase_Letter Lower
           X_POSIX_Lower Lowercase Print X_POSIX_Print Unicode Word X_POSIX_Word
           XID_Continue XIDC XID_Start XIDS 
```

`unichars`从反面探讨性质问题；它没有告诉您一个字符具有哪些属性，而是给出了一个具有特定属性的 Unicode 字符列表:

```
 $ unichars '\p{Cyrillic}
     Ѐ  U+0400 CYRILLIC CAPITAL LETTER IE WITH GRAVE
     Ё  U+0401 CYRILLIC CAPITAL LETTER IO
     Ђ  U+0402 CYRILLIC CAPITAL LETTER DJE
     Ѓ  U+0403 CYRILLIC CAPITAL LETTER GJE
     Є  U+0404 CYRILLIC CAPITAL LETTER UKRAINIAN IE
     Ѕ  U+0405 CYRILLIC CAPITAL LETTER DZE
     І  U+0406 CYRILLIC CAPITAL LETTER BYELORUSSIAN-UKRAINIAN I
     Ї  U+0407 CYRILLIC CAPITAL LETTER YI
     Ј  U+0408 CYRILLIC CAPITAL LETTER JE
     Љ  U+0409 CYRILLIC CAPITAL LETTER LJE 
```

# 结合起来

如果您在命令行上做过文本处理，那么您很有可能熟悉`comm(1)`。这是一个程序，你给两个文本文件和一个选项开关，指示对这些文件中的行执行不同的 set 操作。例如，`comm -13 A B`打印 B 特有的行，`comm -12 A B`打印 A 和 B 中的行。从我的例子中，您可以很好地了解`comm`的使用难度；我总是不得不查阅手册页或坐下来思考我需要进行的调用，以获得我需要的结果。沮丧之余，我对着互联网大声疾呼:

> ![[ɹɑb hɛlt͡s] profile image](img/2ef3410ba97805dd2ce7915b74a03e58.png)【ɹɑb hɛlt͡s】[@ hoelzro](https://dev.to/hoelzro)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)comm(1)有最古怪的方式指定你想要的东西，但它太有用了=/下午 16:37-2015 年 12 月 30 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=682239111978192897)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=682239111978192897)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=682239111978192897)0

幸运的是，一个程序员同事听到了我的请求:

> ![Vladislav Naumov profile image](img/2b983d34b59cf3d88c37e1a677c8985e.png)弗拉迪斯拉夫·瑙莫夫@ vnaum![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ hoelzro](https://twitter.com/hoelzro)COMBINE(1)如果你想比较多组线条，方法更简单。
> apt-get 安装 moreutils2015 年 12 月 30 日下午 17:02[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=682245404579250176)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=682245404579250176)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=682245404579250176)0

自从那决定命运的一天之后，我一次都没有用过`comm`。`combine`执行我所需要的，并且使用如此简单的语法。我上面的例子分别变成了`combine B not A`和`combine A and B`。

# 发现更多工具

`combine`是 moreutils 包的一部分，可在许多平台上获得；我建议看看软件包中的其他工具，看看你是否能找到解决你现有问题的其他工具，或者更好的是，一个你不知道*你有*的问题！

如果您感兴趣，但是您使用其他类型的结构化数据，您可能会发现 GitHub 上的这个存储库很有趣:

[https://github.com/dbohdan/structured-text-tools](https://github.com/dbohdan/structured-text-tools)

你觉得有什么专门的工具能让你的生活变得更轻松吗？如果有，请告诉我！