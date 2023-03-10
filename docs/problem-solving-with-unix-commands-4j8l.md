# 使用 Unix 命令解决问题

> 原文：<https://dev.to/vegarsti/problem-solving-with-unix-commands-4j8l>

(原载[此处](http://vegardstikbakke.com/unix/)。)

我开始意识到 Unix 命令行工具箱绝对可以修复任何与文本争论相关的问题。让我告诉你我遇到的一个问题，以及我如何使用一些 Unix 命令行实用程序来解决它。

## 问题

我正在为我的硕士论文进行研究。和许多统计学家一样，我正在进行大量的模拟。我首先根据一些数字种子模拟一些数据(以确保可重复性)，然后使用一种算法根据这些数据估计一些东西。对于每次模拟运行，我都会创建一些文件，通常如下:

```
dataset-directory/0001_data.csv
dataset-directory/0001_A.csv 
```

有时跑步会失败。在这种情况下，这并不重要:对于任何失败的模拟，我可以再做一次。对于`0001`数据，我成功运行了算法`A`。因此，我也想在算法`B`中使用`0001`数据。但是我需要的是记录哪些运行失败了。

在对大量数据运行算法`A`之后，我得到了一个类似
的大型文件列表

```
dataset-directory/0001_data.csv
dataset-directory/0001_A.csv
dataset-directory/0002_data.csv
dataset-directory/0002_A.csv
dataset-directory/0003_data.csv
dataset-directory/0003_A.csv
dataset-directory/0004_data.csv
dataset-directory/0005_data.csv
dataset-directory/0005_A.csv
dataset-directory/0006_data.csv
dataset-directory/0006_A.csv
dataset-directory/0007_data.csv
dataset-directory/0007_A.csv
dataset-directory/0008_data.csv
dataset-directory/0009_data.csv
dataset-directory/0009_A.csv
...
dataset-directory/0499_A.csv 
```

敏锐的观察者会注意到数据`0004`和`0008`上的算法`A`的文件丢失了。**我怎样才能得到`A`没有成功的所有号码的列表？**

我当然可以手动检查，但是那样容易出错，而且非常无聊。写个程序来做就好多了！

## 解

说得直白一点:没有成功的是从`0001`到`0500`的数字，成功的除外。获得数字列表的一个便捷命令是`seq` :

```
$> seq 10
1
2
3
4
5
6
7
8
9
10 
```

(如果只给出一个数字，暗示序列从`1`开始。也会做你认为它会做的事。)

现在，如果我们可以获得所有成功运行的列表，我们应该能够通过使用打印所有可能数字的`seq`命令交叉检查成功运行的列表来获得我们想要的结果！

大多数命令行实用程序都做一件非常特殊的事情。例如，用`cut`你可以得到每行
上特定位置的字符

```
$> cat text
Lorem ipsum
dolor sit amet
$> cat text | cut -c 2-5
orem
olor 
```

注意这里使用了所谓的管道操作符`|`。如我所说，大多数实用程序都做一件特定的事情，而且做得很好。有趣的是，这些可以结合起来。通过使用这些管道，管道左边的命令的输出被定向到右边的命令。请注意，这些命令将输入视为一串行，这通常非常方便。

我们可以通过将文件列表输入到一个`grep`命令中来获得一个成功的文件名列表，这个命令可以使用正则表达式。由于所有文件都以 4 位数的相同长度开始，我们可以将它们匹配到正则表达式`\d\d\d\d`，连续匹配 4 位数，并将用于`A`算法的文件结尾添加到正则表达式中。要获得每个文件一行的文件列表，我们可以简单地做`ls`。(虽然`ls`在单独调用每个文件时不会给每个文件指定自己的行，但事实证明通过管道传输`ls`的输出会这样做。)

```
$> ls dataset-directory | grep '\d\d\d\d_A.csv'
0009_A.csv
0001_A.csv
0002_A.csv
0005_A.csv
0007_A.csv
0003_A.csv
0006_A.csv
... 
```

出于某种原因，在使用了`grep`之后，这些以混乱的顺序出现。我们可以使用`sort`来解决这个问题。而且我们只对数字感兴趣，所以我们可以使用`cut -c 1-4`来提取数字部分。

```
$> ls dataset-directory | grep '\d\d\d\d_A.csv' | sort | cut -c 1-4
0001
0002
0003
0005
0006
0007
0009
...
0499 
```

这些数字与来自`seq`命令的数字并不完全相同，因为它们是用零填充的。因此，我们编写了一个快速的 Python 脚本来将它们解析为整数。

```
# parse.py import sys

for line in sys.stdin:
    i = int(line)
    print(i) 
```

现在，输入这个脚本将得到我们想要的数字:

```
$> ls dataset-directory | grep '\d\d\d\d_A.csv' | cut -c 1-4 | python3 parse.py
1
2
3
5
6
7
9
...
499 
```

我们快到了。现在我们必须找出如何交叉检查这些数字列表。幸运的是，有一个名为`comm`的命令，它检查两个输入流中字符的 <u>comm</u> 。为了获得如上所示的一系列命令的输入，我们可以对其进行评估并重定向，这可以通过将它包装在`<(...)`中来实现。

```
$> comm <(ls dataset-directory | grep '\d\d\d\d_A.csv' | cut -c 1-4 | python3 parse.py) <(seq 500)
        1
        2
        3
    4
        5
        6
        7
    8
        9
    10
...
    500 
```

这个输出有点令人迷惑。如果我们阅读`comm`(通过做`man comm`)的手册，我们看到`comm`产生三个文本列作为输出:仅文件 1 中的行；仅文件 2 中的行；和两个文件中的行。为了隐藏列 1——它是空的，因为没有数字仅仅来自文件列表——用标志`-1`调用`comm`。由于我们对两个流中的数字都不感兴趣，所以我们也用`-3`标志进行抑制。

```
$> comm -1 -3 <(ls dataset-directory | grep '\d\d\d\d_A.csv' | cut -c 1-4 | python3 parse.py) <(seq 500)
4
8
...
500 
```

我们完事了。

更新:这篇文章在黑客新闻上引起了一些有趣的讨论。有很多方法可以解决这个问题，但我的方法可能不是最好的。请务必查看如何改进的提示。