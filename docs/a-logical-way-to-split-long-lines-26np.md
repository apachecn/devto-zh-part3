# 拆分长行的逻辑方法

> 原文：<https://dev.to/samwho/a-logical-way-to-split-long-lines-26np>

作为程序员，拆分长行是我们每天都要做的事情，但我很少听到关于如何最好地做这件事的讨论。考虑到我们整个行业对“最佳实践”的痴迷，换行符已经设法相对不受审查。

几年前，我学会了一种逻辑的、独立于语言的、最重要的是产生良好结果的分行方法。

## 矩形法

这种方法的核心原则是始终确保您可以围绕一个元素及其所有子元素绘制一个矩形，而不必与任何不相关的元素重叠。结果是相关的事物靠得更近，我们的眼睛很少需要在遥远的位置之间穿梭。

迷茫？我也是。让我们看一个例子。

```
JavacParser parser = parserFactory.newParser(javaInput.getText(), /*keepDocComments=*/ true, /*keepEndPos=*/ true, /*keepLineMap=*/ true); 
```

这一行有 139 个字符长，取自 [google-java-format](https://github.com/google/google-java-format/blob/64242e17f5478eb07a2ca7e409382271765f2524/core/src/main/java/com/google/googlejavaformat/java/Formatter.java#L140-L145) 的源代码。它由若干要素组成:

1.  变量声明。这包括整条生产线。
2.  变量声明分成两半:左边是类型和名称，右边是表达式。
3.  表达式是一个单一的方法调用，可以分成接收者、方法名和它的参数。
4.  最后，每个方法参数都是它自己的元素。包括评论。

很容易在这周围画一个长方形，就一条线。但是如果我们说我们允许的最大行长度是 80，这一行就有点太长了，需要拆分。

### 决定在哪里进行第一次分割

当我们说“一个元素及其所有子元素”时，我们指的是什么编程语言语法通常可以表示为一棵树。我们的例子应该是这样的:

```
 Variable declaration                                                                                                         
       /                    \                                                                                                        
 Type + name             Expression                                                                                                  
                        /          \                                                                                                 
                   Receiver     Method call                                                                                          
                               /           \                                                                                         
                            Name         Arguments 
```

我们想要分割，这样你就可以在每个子树周围画一个矩形，而不接触任何其他子树。

进行第一次拆分的自然位置是在`=` :
之后

```
JavacParser parser =
    parserFactory.newParser(javaInput.getText(), /*keepDocComments=*/ true, /*keepEndPos=*/ true, /*keepLineMap=*/ true); 
```

这通过了矩形测试，因为我们可以在每个元素及其子元素周围画一个矩形，而不会与不相关的元素重叠:

```
┌──────────────────────────────────────────────────────┐
│┌──────────────────────┐                              │
││ JavacParser parser = │                              │
│└─┬────────────────────┴─────────────────────────────┐│
│  │ parserFactory.newParser(javaInput.getText(), ... ││
│  └──────────────────────────────────────────────────┘│
└──────────────────────────────────────────────────────┘ 
```

一个大矩形围绕着整个东西，两个小矩形分别围绕着声明和赋值。请注意，没有矩形与任何其他矩形重叠。

这是一个很好的进步，但是第二行仍然有 118 个字符长，所以需要再次拆分。

在此之前，我想展示一下，如果我们以稍微不同的方式拆分，这将会是什么样子:

```
JavacParser parser = parserFactory.newParser(
  javaInput.getText(), /*keepDocComments=*/ true, /*keepEndPos=*/ true, /*keepLineMap=*/ true); 
```

这没有通过矩形测试:

```
 ┌────────────────────────────────┐     
JavacParser parser = │ parserFactory.newParser(       │
┌────────────────────┘                                │
│ javaInput.getText(), /*keepDocComments=*/ true, ... │
└─────────────────────────────────────────────────────┘ 
```

不可能在`=`的右手边画一个矩形并捕捉它的所有子元素而不捕捉`=`的左手边。rectangle 方法将此标记为不良分割是正确的。从`newParser`到它的第一次辩论有一段可怕的漫长的路要走，这可能会导致你的眼睛不必要地来回扫视。

### 决定在哪里进行第二次分割

有几种方法可以进行第二次分割，但我最喜欢的方法是:

```
JavacParser parser =
    parserFactory.newParser(
        javaInput.getText(), /*keepDocComments=*/ true, /*keepEndPos=*/ true, /*keepLineMap=*/ true); 
```

让我们看看它周围有一些矩形的样子:

```
┌────────────────────────────────────────────────────────────┐
│┌──────────────────────┐                                    │
││ JavacParser parser = │                                    │
│└─┬────────────────────┴─────────────────────────────┐      │
│  │ parserFactory.newParser(                         │      │
│  └─┬────────────────────────────────────────────────┴────┐ │
│    │ javaInput.getText(), /*keepDocComments=*/ true, ... │ │
│    └─────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────┘ 
```

我们还是好的！请注意，由于空间限制，上面没有在每个元素周围画出一个矩形。你也可以从`parserFactory.newParser`开始画一个矩形，围绕其后的所有东西。

另一种也能通过矩形测试的分割方法是:

```
JavacParser parser =
    parserFactory
        .newParser(
            javaInput.getText(), /*keepDocComments=*/ true, /*keepEndPos=*/ true, /*keepLineMap=*/ true); 
```

但是在`.`分手对我来说有点太急切了。你可以使用更少的垂直空间，并且保持线条清晰。

遗憾的是，我们的第三行仍然有 94 个字符长，需要再次拆分。

## 决定在哪里进行第三次分割

同样，这里有多条路线，但我会选择以下路线:

```
JavacParser parser =
    parserFactory.newParser(
        javaInput.getText(),
        /*keepDocComments=*/ true,
        /*keepEndPos=*/ true,
        /*keepLineMap=*/ true); 
```

带矩形:

```
┌──────────────────────────────────┐
│┌──────────────────────┐          │
││ JavacParser parser = │          │
│└─┬────────────────────┴─────┐    │
│  │ parserFactory.newParser( │    │
│  └─┬────────────────────────┴───┐│
│    │ javaInput.getText(),       ││
│    ├────────────────────────────┤│
│    │ /*keepDocComments=*/ true, ││
│    ├────────────────────────────┤│
│    │ /*keepEndPos=*/ true,      ││
│    ├────────────────────────────┤│
│    │ /*keepLineMap=*/ true);    ││
│    └────────────────────────────┘│
└──────────────────────────────────┘ 
```

同样，由于空间原因，没有画出所有可能的矩形。

我们也可以每行有多个参数:

```
JavacParser parser =
    parserFactory.newParser(
        javaInput.getText(), /*keepDocComments=*/ true,
        /*keepEndPos=*/ true, /*keepLineMap=*/ true); 
```

这通过了矩形测试，并且没有一行超过 80 个字符的限制。然而，出于个人喜好，我通常会避免这样做。

## 结论

我的大部分代码都遵循这种风格，我觉得这样更容易阅读。我相信这只是众多方法中的一种，我很想听听它们，以及它们与这种方法相比如何！