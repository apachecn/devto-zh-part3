# Phred 质量分数

> 原文：<https://dev.to/robertopreste/phred-quality-score-15f8>

下一代测序技术为组学数据分析带来了新的见解，这主要归功于它们在检测生物变异方面的可靠性。这种可靠性通常用一个叫做 [Phred 质量分数](https://en.wikipedia.org/wiki/Phred_quality_score)(或 Q 分数)的值来衡量。

碱基的 Phred 分数是一个整数值，表示碱基调用中错误的估计概率。数学上，Q 分数与碱基调用错误概率 P 成对数关系，并且可以使用以下公式计算:

> Q = -10 log10 P

在现实世界中，质量分数为 20 意味着在 100 分中有可能基数不正确；质量分数为 40 意味着碱基被错误调用的几率是万分之一。

Phred 分数也与碱基判断准确性成反比，因此较高的 Q 分数意味着更可靠的碱基判断。下面是一个有用的表格，显示了这种简单的关系:

| Phred 质量分数 | 不正确的碱基调用问题 | 基本呼叫准确性 |
| --- | --- | --- |
| Ten | 十分之一 | 90% |
| Twenty | 百分之一 | 99% |
| Thirty | 千分之一 | 99.9% |
| Forty | 万分之一 | 99.99% |

在 [fastq](https://dev.to/robertopreste/counting-sequences-in-fasta-fastq-files-6oo) 文件中，Phred 质量分数通常用 [ASCII 字符](https://en.wikipedia.org/wiki/ASCII)表示，这样每个碱基的质量分数可以用单个字符指定。虽然较旧的 Illumina 数据用于应用 ASCII_BASE 64，但现在 ASCII_BASE 33 表已普遍用于 NGS 数据:

| q 分数 | ASCII 字符 | q 分数 | ASCII 字符 | q 分数 | ASCII 字符 | q 分数 | ASCII 字符 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | ！ | Eleven | , | Twenty-two | seven | Thirty-two | A |
| one | " | Twelve | - | Twenty-three | eight | Thirty-three | B |
| Two | # | Thirteen | 。 | Twenty-four | nine | Thirty-four | C |
| three | $ | Fourteen | / | Twenty-five | ： | Thirty-five | D |
| four | % | Fifteen | Zero | Twenty-six | ； | Thirty-six | E |
| five | & | Sixteen | one | Twenty-seven | < | Thirty-seven | F |
| six | ' | Seventeen | Two | Twenty-eight | = | Thirty-eight | G |
| seven | （ | Eighteen | three | Twenty-nine | > | Thirty-nine | H |
| eight | ) | Nineteen | four | Thirty | ？ | Forty | 我 |
| nine | * | Twenty | five | Thirty-one | @ | Forty-one | J |
| Ten | + | Twenty-one | six |  |  |  |  |

尽管有很多 Python、 [Biopython](https://biopython.org) 和独立的软件来处理 Phred 质量分数，但是一个简单的命令可以将一个 ASCII 字符转换成相应的质量分数，如下所示(从终端):

```
python3 -c 'print(ord("<ASCII>")-33)' 
```

Enter fullscreen mode Exit fullscreen mode

或者，在 Python3 会话中工作时:

```
print(ord("<ASCII>")-33) 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下，只要用实际的 ASCII 字符替换`<ASCII>`就可以了。