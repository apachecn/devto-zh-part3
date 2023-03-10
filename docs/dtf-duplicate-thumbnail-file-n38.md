# DTF -重复缩略图文件

> 原文：<https://dev.to/victorqribeiro/dtf-duplicate-thumbnail-file-n38>

# DTF

重复缩略图文件——做[系统综述](https://en.wikipedia.org/wiki/Systematic_review)时识别重复文章的一种方法。

## 问题

当在不同的数据库上运行你的搜索字符串时，一个系统的综述可以产生无数的文章。许多时候，文章被导出为 PDF 文件并提交给不同的期刊或出版物。问题是，根据 PDF 导出的方式，它将生成不同的文件，这使得很难通过查看它们的哈希来检查重复。

## 实验

写了五篇文章导出成 PDF，每篇都有很小的差别。

*   **01-jpg-100.pdf**-以 100%质量的 JPG 文件导出。

*   **02-lossless.pdf**-导出为无损文件。

*   **03-lossless-2newlines.pdf**-导出为无损文件，在文件末尾增加两行。

*   **04-jpg-80-2newlines.pdf**——导出为 JPG 文件，质量 80%，文件结束后新增两行。

*   **05-jpg-80.pdf**-以 100%质量的 JPG 文件导出。

让我们看看他们的散列:

| 混杂 | 文件 |
| --- | --- |
| 1 db 6 c 720061004 b 740 b 87320 f 0 D1 D2 a 68 BD 9d 312 | 01-jpg-100.pdf |
| c 9 ECD 0d 02 f 04637 ab 81 f 8f 383 a 74929d 8 a9 f1 a 40 | 02-lossless.pdf |
| DD 8 f 35 b 79 ded 6 b 61 f 53 ea EB 0 a 9 a 7 f1 BF 34 EAE 6 | 03-lossless-2newlines.pdf |
| 9b 660130 bb a4 C2 c 7 b 63 be 832 aab 08 a 48 a2 B2 E6 f 5 | 04-jpg-80-2newlines.pdf |
| ffc 4a 3 fcff 380929 f7c 38379 f 5804 e 7 bbd 87 ea 44 | 05-jpg-80.pdf |

根据每个文件的散列，它们互不相同，而实际上是“相同的”文档。

## 解

如果我们不看每个文件的散列，而是看它们的缩略图，会怎么样？可能会好一点。

因此，编写了一个 python 脚本来生成每个文件的缩略图，并测量这些缩略图之间的差异。

结果如下:

| 文件 01 | 文件 02 | 差异 |
| --- | --- | --- |
| 03-lossless-2newlines.png | 02-lossless.png | Zero |
| 03-lossless-2newlines.png | 05-jpg-80.png | Zero |
| 03-lossless-2newlines.png | 01-jpg-100.png | Zero |
| 03-lossless-2newlines.png | 04-jpg-80-2newlines.png | Zero |
| 02-lossless.png | 05-jpg-80.png | Zero |
| 02-lossless.png | 01-jpg-100.png | Zero |
| 02-lossless.png | 04-jpg-80-2newlines.png | Zero |
| 05-jpg-80.png | 01-jpg-100.png | Zero |
| 05-jpg-80.png | 04-jpg-80-2newlines.png | Zero |
| 01-jpg-100.png | 04-jpg-80-2newlines.png | Zero |

所以，我们可以看到，它们之间有 0.0 的差别，所以它们必须有相同的内容。

## 结论

这个实验提供了一个不同的方法来解决系统回顾中的一个常见问题，在这个特殊的例子中，它的效果更好。该方法可以与其他方法一起使用，以识别和排除重复文件。

可以将 *dtf.py* 脚本用作更健壮的脚本的基础，该脚本可以比较任何可以直观比较的文件。

实验的代码是[这里是](https://github.com/victorqribeiro/dtf/)