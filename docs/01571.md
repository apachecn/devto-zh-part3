# Fasta/Fastq 文件中的计数序列

> 原文：<https://dev.to/robertopreste/counting-sequences-in-fasta-fastq-files-6oo>

一个公认的生物信息学家通常有一些适当的信息学工具来处理和分析基因组数据，例如对文件中的序列进行计数。

尽管如此，在某些情况下，依靠标准的 Unix 命令可能是有用的，例如，当您可信赖的笔记本电脑不可用或者您正在其他人的机器上工作时。

* * *

## FASTA files

一个[。fasta 文件](https://en.wikipedia.org/wiki/FASTA_format)是一个简单的纯文本文件，其中每个序列都由一个标题行表示，以>开始，包含序列标识符和细节，后面是许多包含实际序列的行:

```
>SEQUENCE_1
MTEITAAMVKELRESTGAGMMDCKNALSETNGDFDKAVQLLREKGLGKAAKKADRLAAEG
LVSVKVSDDFTIAAMRPSYLSYEDLDMTFVENEYKALVAELEKENEERRRLKDPNKPEHK
IPQFASRKQLSDAILKEAEEKIKEELKAQGKPEKIWDNIIPGKMNSFIADNSQLDSKLTL

>SEQUENCE_2
SATVSEINSETDFVAKNDQFIALTKDTTAHIQSNSLQSVEELHSSTINGVKFEEYLKSQI
ATIGENLVVRRFATLKAGANGVVNGYIHTNGRVGVVIAAACDSAEVASKSRDLLRQICMH 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果您想计算包含在. fasta 文件中的序列数，您可以使用`grep`命令:
轻松完成

```
grep ">" file.fasta | wc -l 
```

Enter fullscreen mode Exit fullscreen mode

这一行所做的只是选择所有的`>`字符，然后统计它们出现的次数。更具体地说，grep 命令将找到所有以`>`开头的行，然后它的输出将被传送到`wc`(字数统计)命令，由于有了`-l`选项，它将计算行数而不是字数。

在现代系统上使用`grep`的另一种方式是使用下面的命令:

```
grep -c ">" file.fasta 
```

Enter fullscreen mode Exit fullscreen mode

`-c`选项将指示命令对匹配行进行计数，而不是仅仅将它们打印到屏幕上，不需要如上所述的`wc -l`。

## FASTQ 文件

和[一起工作并不少见。fastq 文件](https://en.wikipedia.org/wiki/FASTQ_format)也是，不知何故就像。fasta 文件，但他们也报告基地质量。在本例中是字符`>`,用于指定。fasta 文件，被替换为`@`；然而，如上所示搜索它的出现可能会产生误导，因为`@`字符也被用作[质量分数](https://dev.to/robertopreste/phred-quality-score-15f8)符号。

无论如何，在. fastq 文件中有一个计数序列的技巧，它与这类文件的通常布局有关。每个序列由四行表示:第一行是序列标识符，第二行是实际序列，第三行通常是空的，只包含一个占位符`+`，而最后一行包含序列质量分数:

```
@SEQ_ID1
GATTTGGGGTTCAAAGCAGTATCGATCAAATAGTAAATCCATTTGTTCAACTCACAGTTT
+
!''*((((***+))%%%++)(%%%%).1***-+*''))**55CCF>>>>>>CCCCCCC65 
```

Enter fullscreen mode Exit fullscreen mode

这意味着计算序列的数量比预期的要容易，只需要将文件中的行数除以 4。这可以在 Bourne shells 上使用以下命令来完成:

```
LINES=`cat file.fastq | wc -l`
READS=`expr $LINES / 4`
echo $READS 
```

Enter fullscreen mode Exit fullscreen mode

在现代 shells 上，比如 Bash，这可以用一个简单的一行程序来完成:

```
expr $(cat file.fastq | wc -l) / 4 
```

Enter fullscreen mode Exit fullscreen mode

* * *

通过这些简单的技巧，您可以很容易地找到。fasta 或者。fastq 文件，直接来自您的 Unix shell。