# 解决 VS 错误 CS0030 无法将类型“Data[]”转换为“Data”

> 原文：<https://dev.to/rachelsoderberg/resolving-vs-error-cs0030-cannot-convert-type-data-to-data-453a>

大多数时候，Visual Studio 能够很好地告诉我们语法错误和其他 bug 隐藏在哪里。虽然偶尔(甚至在我们的技术领导警告我们要注意他们提到的“那一个错误”之后)，即使是我们中最优秀的人也会被难倒。一些最具挑战性的错误可能是运行时错误——尤其是那些隐藏在代码库深处的错误，不会导致任何 Intellisense 警告或错误，也不是开发人员造成的。对于 CS0030 错误，它发生在一个生成的文件中，在该文件中警告您“对此文件的更改可能会导致不正确的行为”。

当我遇到这个问题时，可能过了一段时间后，我才明白我的技术主管臭名昭著的“双括号错误”正是我在我的项目中添加我们的 Salesforce web 引用时所遇到的问题。我也意识到其他人可能和我有同样的困扰，我应该写下来分享。在这篇文章中，我不会过于专业；我的主要目标是把问题和解决方案以书面形式表达出来。

现在来看问题！不管出于什么原因，当添加一些 web 引用时，Visual Studio 决定在生成引用类文件时加倍提供帮助，并构建如下内容:

[https://thepractical dev . S3 . Amazon AWS . com/I/6v7l 84 AZS 423j 9 EC 3 ood。PNG](https://thepracticaldev.s3.amazonaws.com/i/6v7l84azs423j9ec3ood.PNG)

(注意两对括号，其中一个我突出显示了。那个高亮的不应该在那里。)

一旦你遇到过一两次这种情况，解决它就像是你的第二天性，但是对于那些第一次参加 CS0030 竞技表演的人，我想分享一下我是如何发现并解决这个问题的:

*   首先，我检查了 StackOverflow，并在这里找到了许多关于它为什么会发生在 XmlElementAttribute 上的有用信息，而且它并不罕见:[https://stack overflow . com/questions/6678934/unable-to-generate-a-temporary-class-result-1-error-cs 0030-cannot-convert-ty](https://stackoverflow.com/questions/6678934/unable-to-generate-a-temporary-class-result-1-error-cs0030-cannot-convert-ty)
*   其次，我转到我的 Salesforce web 参考类文件并搜索“[][]”。你瞧，在大约 255k 行中，出现了一对一对的括号！删除两对中的一对解决了这个问题，允许我继续愉快地运行和测试我的代码。

总之，如果您最近向 C#项目添加了一个或多个 web 引用，而 Visual Studio 给了您一个错误代码 CS0030，请查看它们的引用类并搜索双括号“[][]”。你可能有一两副备用的藏在里面。