# 一些非常酷的命令行工具，可以提高你的工作效率。

> 原文：<https://dev.to/shaminm07/some-really-cool-command-line-utilities-which-increase-your-productivity-1bga>

bash 中有许多命令行实用程序可以帮助您在几秒钟内处理数百万的数据。其中一些是

## 头

Head 是一个命令行实用程序，用于打印标准输入
`head -1 data.txt`中给出的文件的第一部分——打印 data.txt 文件的第一行

[阅读更多](https://shapeshed.com/unix-head/)

## 尾巴

Tail 是一个命令行实用程序，用于打印标准输入中给出的文件的最后一部分。它的作用正好与头部相反

`tail -1 data.txt` -打印 data.txt 文件的最后一行

[阅读更多](https://shapeshed.com/unix-tail/)

## 猫

Cat 是一个命令行实用程序，用于连接文件和打印到标准输出。

`cat data.txt` -打印 data.txt 文件的内容

[阅读更多](https://shapeshed.com/unix-cat/)

## Grep

Grep 是一个命令行实用程序，用于打印与模式匹配的行。

`cat data.txt | grep "some-data"` -在 data.txt 文件中搜索`some-data`，并打印包含文本的行

[阅读更多](https://shapeshed.com/unix-grep/)

## 舒夫

Shuf 是一个命令行工具，它可以从输入行到标准输出行生成随机排列。

`cat data.txt | shuf` -随机打印 data.txt 的内容

[阅读更多](https://shapeshed.com/unix-shuf/)

## Uniq

Uniq 是一个命令行实用程序，可用于过滤文件中重复的行。

`cat data.txt | uniq` -过滤 data.txt 文件中的重复行并打印。

[阅读更多](https://shapeshed.com/unix-uniq/)

## 切

Cut 是一个命令行实用程序，用于从文件的每一行中剪切部分，并将结果写入标准输出。

`echo 'baz' | cut -b 2` -打印切割中指定的秒字字符`a`。

[阅读更多](https://shapeshed.com/unix-cut/)

## 加入

Join 是一个命令行工具，用于连接公共字段上的两个文件的行。

`join file1.txt file2.txt` -根据一些公共字段连接两个文件的数据。

[阅读更多](https://shapeshed.com/unix-join/)

## 分割

Split 是一个命令行工具，可用于将文件分割成多个部分。

`split -l 200 split_me.txt` -将 split_me.txt 拆分成 n 个 200 行的文件

[阅读更多](https://www.folkstalk.com/2012/06/split-command-examples-in-unix-linux.html)

## jq

jq 是一个用于处理 JSON 的命令行实用程序。

`jq '.' data.json` - Pretty prints data.json 文件

[阅读更多](https://shapeshed.com/jq-json/)

## awk

awk 是一个命令行实用程序，它在该行上搜索特定的模式和指定的操作。

[阅读更多](https://www.gnu.org/software/gawk/manual/gawk.html#toc-Part-I_003a-The-awk-Language)