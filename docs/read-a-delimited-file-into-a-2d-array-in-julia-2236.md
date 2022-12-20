# 将分隔文件读入 Julia 中的 2D 数组

> 原文：<https://dev.to/rpalo/read-a-delimited-file-into-a-2d-array-in-julia-2236>

这是一个巧妙的技巧，我花了一点时间才弄明白。许多代码挑战的出现都是从读入一个文本文件开始的，这个文本文件是一个 2D 字符网格。我想知道如何在朱莉娅身上做到这一点。我想展示几个案例。

## 第一种情况:分隔

这个案例简单多了，也更令人满意。假设您有一个名为 data.txt 的文本文件，如下所示:

```
A B C A
D B A C
C C C B
D A B B 
```

Enter fullscreen mode Exit fullscreen mode

我们如何将它读入 Julia 的 2D 数组(矩阵),以利用 Julia 语法的并行处理能力呢？

事实证明，在标准库中有一个这样的函数:[`DelimitedFiles`包。](https://docs.julialang.org/en/v1/stdlib/DelimitedFiles/index.html#DelimitedFiles.readdlm-Tuple%7BAny,AbstractChar,Type,AbstractChar%7D)T3】

```
using DelimitedFiles

grid = readdlm("data.txt", Char)

@show grid 
```

Enter fullscreen mode Exit fullscreen mode

`readdlm`是一个很酷的函数，它为类型、分隔符、eol 字符等提供了一系列可选参数。下面是运行脚本的输出:

```
~/temp
▶ julia delim.jl
grid = ['A' 'B' 'C' 'A'; 'D' 'B' 'A' 'C'; 'C' 'C' 'C' 'B'; 'D' 'A' 'B' 'B'] 
```

Enter fullscreen mode Exit fullscreen mode

看到这有多简单了吗？处理行尾和更多！

## 第二种情况:Smooshed 字符

现在，如果字符没有用*分隔，而是全部连在一起，会发生什么呢？事情变得有点困难，我们实际上必须做一些处理。我将向您展示最终的解决方案，然后讨论每个关键部分。*

这里是`smooshed_data.txt` :

```
ABCA
DBAC
CCCB
DABB 
```

Enter fullscreen mode Exit fullscreen mode

我们需要做的是处理它:

```
lines = map(collect, readlines("smooshed_data.txt"))

grid = permutedims(hcat(lines...))

@show grid 
```

Enter fullscreen mode Exit fullscreen mode

并运行它:

```
~/temp
▶ julia smooshed.jl
grid = ['A' 'B' 'C' 'A'; 'D' 'B' 'A' 'C'; 'C' 'C' 'C' 'B'; 'D' 'A' 'B' 'B'] 
```

Enter fullscreen mode Exit fullscreen mode

稍微复杂一点，但仍然只有 3 行相对较短的代码。让我们来谈谈它是如何工作的:

1.  我们使用`readlines`将文本文件中的所有文本读入一个数组。这是打开文件并一次读取一行的便捷方式。其中一个很好的特性是它为我们处理了移除换行符的问题——不需要清理！

2.  我们`map`在那排线上，`collecting`每条线。`collect`接受它的参数并遍历它，将各个片段收集到一个数组中。在我们的例子中，由于每一行都是一个字符串，当我们迭代它时，我们得到一串字符。所以，完整地说，这一行转换了代表文件中每一行的字符串数组，并将其转换为字符数组的数组。

3.  不幸的是，一维数组被保存为列，而不是行，这意味着 Julia 认为它们的形状是 N x 1，而不是 1 x N。出于这个原因，实际上更容易将字符数组一列接一列地组合在一起，我们使用`hcat`来实现这一点。`hcat`并排连接数组，并以任意数量的数组作为其参数。出于这个原因，我们需要使用`...`语法将`lines`数组*扩展*为`hcat`。

4.  一样的台词。因为我们现在在数据文件中有了并排的字符行，所以当前的表示是这样的:

```
A D C D
B B C A
C A C B
A C B B 
```

Enter fullscreen mode Exit fullscreen mode

第一列匹配我们的文本文件的第一行，等等。这与我们想要的相差甚远。我们使用`permutedims`来翻转行和列。在其他语言中，您可能会认为这是一种“转置”出于可以理解但令人沮丧的原因，在 Julia 中，实际的`transpose`函数是递归的，用于线性代数，所以`transpose(matrix)`似乎只对数字有效。

值得注意的是，我们还可以为每一行设置`permutedims(line)`，以便在使用`vcat`调用将它们串联成行之前，从 N x 1 转换为 1 x N。这更直观，但是我不确定它对性能的影响。好像应该慢一点。

无论如何，我们最终得到了我们所希望的，一个可以处理的 2D 字符数组。希望能更好地理解一些实用的朱莉娅基础知识！

## 寻找帮助

我正在学习 Julia，我已经知道了一些方法来做我想做的事情，但是我真的不知道什么是习惯用法或者最好的方法。如果任何人除了官方文档(很好)或初学者“Julia 介绍”类型的资源之外还有一些有用的 Julia 资源，请告诉我它们是什么！