# 上下文无关的语法教程

> 原文：<https://dev.to/vicentemaldonado/a-context-free-grammar-tutorial-38b5>

[![](img/7b6b9326200328cb3bcc7ec1a5072a44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LKkZoEoi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AX769PvIY7YQN4lTge1Zt6A.jpeg)

我最近看到了一个关于上下文无关语法的教程，里面有几个你可以在这些语法中找到的常见模式的例子，我对自己说——为什么不实现其中的一些例子作为练习呢？

我首先必须选择语言、Java 和工具来实现这些例子，用 [JFlex](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=2ahUKEwiv5YKW8vrgAhWriIsKHR3dAg4QFjAAegQIBRAC&url=https%3A%2F%2Fwww.jflex.de%2F&usg=AOvVaw1UsT-Zk-dlb95doMmPXcLF) 和 [Jacc](http://web.cecs.pdx.edu/~mpj/jacc/) 。这一对与 Flex 和 Bison 非常相似，希望语法不会因为实现而变得模糊。

我熟悉了 JFlex 和 Jacc，并做了三个关于使用它们的笔记:

*   [认识 JFlex](https://dev.to/wurdlack/meet-jflex-59gd) —用一个独立 lexer 的小例子介绍 JFlex，
*   [使用 JFlex 计算单词数](https://dev.to/wurdlack/use-jflex-to-count-words-5ci4)——另一个独立的 lexer 示例，这次有点复杂，
*   [一起使用 JFlex 和 Jacc](https://dev.to/wurdlack/use-jflex-and-jacc-together-3nnd)—如何让 JFlex 和 Jacc 协同工作，也有一个初步的例子。

如果您对 CFG 和解析感兴趣，我提到的教程是一个不错的地方，可以获得一些实现它们的实际经验。你可以在这里找到它[，我也把它上传到了](http://marvin.cs.uidaho.edu/Handouts/) [Github](https://github.com/Wurdlack/Medium/tree/master/cfg_tutorial) (希望没有违反任何版权法)。

我用来演示 JFlex 和 Jacc 如何协同工作的语法实际上是本教程第 2.1 节的语法(“允许 X 列表的语言语法”)，所以我实际上已经开始练习了。耶。

使用 Graphviz，你可以得到你的语法的可视化表示。首先将语法导出为一个点文件:

```
jacc -d Parser.jacc 
```

这将创建 Parser.dot. Then

```
dot -Tjpg Parser.dot -o Parser.jpg 
```

最后你和 Parser.jpg 在一起。很简单:

[![](img/4ec30d04ec304e040c5de986dab612bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZgsTSMjv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/307/1%2AbFCKrWMJPtYDgJIIml8Agg.png)

它是一个用有向图表示的状态机。创建语法可视化表示只是 Jacc 提供的帮助您调试语法的工具之一。您可以将语法导出到文本文件:

```
jacc -v Parser.jacc 
```

这将创建 Parser.output:

```
// Output created by jacc on Wed Mar 13 09:15:29 CET 2019

state 0 (entry on sentence)
    $accept : \_sentence $end

X shift 2
    . error

sentence goto 1

state 1 (entry on sentence)
    $accept : sentence\_$end
    sentence : sentence\_X (2)

$end accept
    X shift 3
    . error

state 2 (entry on X)
    sentence : X\_ (1)

$end reduce 1
    X reduce 1
    . error

state 3 (entry on X)
    sentence : sentence X\_ (2)

$end reduce 2
    X reduce 2
    . error

4 terminals, 1 nonterminals;
2 grammar rules, 4 states;
0 shift/reduce and 0 reduce/reduce conflicts reported. 
```

或者转换成 html 文件:

```
jacc -h Parser.jacc 
```

这将创建 ParserMachine.html:

#### 分析器的生成机器

```
// Output created by jacc on Wed Mar 13 09:20:03 CET 2019

    $accept : _sentence $end

    X shift 2
    . error

    sentence goto 1

    $accept : sentence_$end
    sentence : sentence_X    (2)

    $end accept
    X shift 3
    . error

    sentence : X_    (1)

    $end reduce 1
    X reduce 1
    . error

    sentence : sentence X_    (2)

    $end reduce 2
    X reduce 2
    . error

4 terminals, 1 nonterminals;
2 grammar rules, 4 states;
0 shift/reduce and 0 reduce/reduce conflicts reported.

```

您实际上可以通过单击链接来浏览机器状态。

Jacc 还支持在样本输入上跟踪语法，并在语法中嵌入定制的错误结果——仅此而已。ttfn！