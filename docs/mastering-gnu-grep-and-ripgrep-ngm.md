# 掌握 GNU grep 和 ripgrep

> 原文：<https://dev.to/learnbyexample/mastering-gnu-grep-and-ripgrep-ngm>

你好！

我最近出版了一本关于 GNU GREP 和 RIPGREP 的书

[![grep cover image](img/c188fa95e7a27d3f3ef8a0b5bbb204df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uPDH4wcL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://learnbyexample.github.img/books/grep.png)

使用以下任一网站获取电子书:

*   [https://gumroad.com/l/gnugrep_ripgrep](https://gumroad.com/l/gnugrep_ripgrep)
*   [https://leanpub.com/gnugrep_ripgrep](https://leanpub.com/gnugrep_ripgrep)

您也可以使用以下链接获得电子书，作为**神奇的一行程序**捆绑包的一部分:

*   [https://gumroad.com/l/oneliners](https://gumroad.com/l/oneliners)
*   [https://leanpub.com/b/oneliners](https://leanpub.com/b/oneliners)

您也可以使用以下链接获得作为 **Awesome Regex** 捆绑包一部分的电子书:

*   [https://gumroad.com/l/regex](https://gumroad.com/l/regex)
*   [https://leanpub.com/b/regex](https://leanpub.com/b/regex)

**注意**请使用有效的电子邮件并保存您收到的邮件，您将获得未来版本的免费更新。

代码片段、示例文件、示例章节等可在[https://github.com/learnbyexample/learn_gnugrep_ripgrep](https://github.com/learnbyexample/learn_gnugrep_ripgrep)获得

这本书涵盖了`GNU grep`和`ripgrep`的特点以及对 BRE/ERE/PCRE(2)/Rust `regular expressions`的讨论。例子被广泛使用，请跟着打出来。练习也包括测试你的理解。

你们中的一些人可能熟悉我的命令行文本处理报告。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[learn 示例](https://github.com/learnbyexample) / [命令行文本处理](https://github.com/learnbyexample/Command-line-text-processing)

### ⚡从寻找文本到搜索和替换，从排序到美化文本等等🎨

<article class="markdown-body entry-content container-lg" itemprop="text">

# 命令行文本处理

了解可用于常见和特殊文本处理需求的各种命令。示例已经在 GNU/Linux 上测试过了——其他发行版的语法/特性可能会有变化，详情请参考各自的`man`页面。

* * *

<g-emoji class="g-emoji" alias="warning" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a0.png">⚠️</g-emoji> <g-emoji class="g-emoji" alias="warning" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a0.png">⚠️</g-emoji> 我不再积极参与这项回购。相反，我一直在将现有章节转换成电子书(链接见下面的[电子书章节](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/#ebook))，在相同的许可下可用。这些电子书格式更好，更新了软件的新版本，包括练习、解答等。一旦所有的章节都被转换，我会存档这个回购。

* * *

## 章

*   [猫，少，尾巴和头](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/./tail_less_cat_head.md)
    *   猫，少，尾巴，头，文本编辑器
*   [GNU 抓住了](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/./gnu_grep.md)
*   [GNU sed](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/./gnu_sed.md)
*   [GNU awk](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/./gnu_awk.md)
*   [佩尔的瑞士小刀](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/./perl_the_swiss_knife.md)
*   [Ruby one 内胆](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/./ruby_one_liners.md)
*   [整理物品](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/./sorting_stuff.md)
    *   排序，唯一，通信，随机
*   [重组正文](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/./restructure_text.md)
    *   粘贴、列、pr、折叠
*   [有什么不同](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/./whats_the_difference.md)
    *   cmp，差异
*   [文件属性](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/./file_attributes.md)
    *   厕所，你，df，触摸，文件
*   [杂项](https://raw.githubusercontent.com/learnbyexample/Command-line-text-processing/master/./miscellaneous.md)
    *   cut, tr, basename, dirname, xargs, seq

…

</article>

[View on GitHub](https://github.com/learnbyexample/Command-line-text-processing)

这本书是在第`grep`章的基础上编辑的，增加了更多的描述、更好的例子、练习等。此外，在撰写这本书的时候，我大胆地去查阅了`ripgrep`。我假设它会和`gnu grep`相似，我只需要简单的改变就能在书中呈现出来。嗯，结果是更多的工作，我不得不满足于没有涵盖所有的选项和定制。事后看来，如果我写一本独立的书会更好。就速度而言，`ripgrep`给人印象非常深刻，并且有很多额外的好功能。例如:`ripgrep`的多行`-U`和替换`-r`选项非常方便——在某些情况下，你可以用这个选项代替`sed`来增加速度、`-F`选项和更好的正则表达式特性。

**目录**

*   前言
*   介绍
*   常用选项
*   BRE/ERE 正则表达式
*   上下文匹配
*   递归搜索
*   杂项选项
*   Perl 兼容的正则表达式
*   陷阱和技巧
*   ripgrep
*   进一步阅读

希望你觉得这本书有用。我将非常感谢您的反馈、书评和建议。

快乐学习:)