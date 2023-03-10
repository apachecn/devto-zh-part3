# 初学者 Shell 脚本生存手册

> 原文：<https://dev.to/danilogila/manual-de-sobrevivncia-do-shell-script-para-iniciantes-17kn>

<figure>

[![Markdowm Image](img/349407d3989a97e8d28c23b677cf89ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FQKXVZEl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danilogila.github.io/asseimg/linux_terminal_wallpaper.png)

<figcaption class="caption">Foto por Digital Ocean</figcaption>

</figure>

# 简介:

我还记得第一次看到 Linux 终端时。我很惊讶他们在那奇怪的黑屏上有这么多信息，这让我有些害怕。随着时间的推移，我开始学习和了解 Shell 的控制，我意识到掌握这个终端对于我的日常工作和整个职业生涯来说将是多么的有生产力。

这个帖子的目的是与 Shell 脚本分享我的一些进化，并表明我们不必害怕使用它。我们将看到，最终，他将成为我们工作效率的重要合作伙伴:)

### 首先……

在我们开始之前，打开您喜欢的终端。之后，我们将开始我们的外壳研究。我们将看到您可以在目录之间导航、创建文件等。

* * *

### 列出文件和目录

要列出/查看目录中的文件和文件夹，只需使用命令`ls`。如果仔细查看，您将看到文件以不同于文件夹的颜色列出。视您使用的终端机而定，会以类似的方式显示，以便于检视内容。如果终端不这样显示，请使用`ls --color`。

[![Markdowm Image](img/4179140198eedae0d3d4aee98ad98135.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LwT096kT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ztjzrhgca5n48uy7kuao.gif)

### 更改目录

当我们要更改目录时，我们使用 cd 目录名称命令。此外，我们可以使用`cd ..`切换目录。`..`告诉我们，我们正在恢复目录层次结构中的一个级别。也就是说，在 Download 文件夹中使用“`cd ..`”时，我们将返回 tutorial-shell 文件夹。

[![Markdowm Image](img/1cbf397520bc80341524d51be1f46fe6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kZbCIf7J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d3banryqktfczwjpx18w.gif)

### 创建和删除目录

用于创建和删除文件夹的命令非常相似。当我们要创建目录时，我们使用了[我们使用`rmdir nome-do-diretório`删除目录。值得一提的是，如果要删除的目录中有任何内容，则必须在目录名称前使用“`-r`”标记。这可以确保删除所有子文件夹和文件，但使用此命令时要谨慎，因为执行此命令后，它将删除所有文件，而不会提示您进行确认或其他操作。

[![Markdowm Image](img/8bd149d4bc2736876c936aa3fff88e8f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kaskllKJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hjz9xw0jnsh0n5lu55gz.gif)

### 创建和删除文件

我们使用`touch nome-do-arquivo`命令创建文件，并可以根据文件类型通过放置扩展名来创建文件。示例:`touch index.html`创建 HTML 文件。可以对各种不同类型的文件执行此操作。要删除，我们使用命令`rm nome-do-arquivo`。

[![Markdowm Image](img/58332a7cf4019863fc2d276ce98d71aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--od8CRHCx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/076vhmtjaam2qzj4ethw.png)

### 编辑档案

Nano 是我们终端使用的文字编辑器。此编辑器的主要优点是，它专注于对所需文件进行文本编辑的简单性。要编辑文件，只需键入“`nano nome-do-arquivo`”，编辑器将在终端内打开。

一旦打开，nano 将为我们提供各种编辑命令。只需使用所需的功能并以最方便的方式编辑文件即可。

除了 nano，我们还有其他编辑，比如我看到的，我来了，等等，但这是为了另一篇文章；）

[![Markdowm Image](img/9dda9dd3b47acd25f14e519b42317dea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bPpwT3g7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8gtwz9wo0h97stsj6619.png)

### 查看文件内容

在各种显示命令中，我分离了我每天最常用的 4 个命令。他们是:

`More`:一次显示一页文档。示例:更多影片. txt。

`Less`:少命令与多命令相似。它们之间的区别在于，您可以搜索文件中的特定文本，可以在页面之间导航，而使用 more 则无法执行此操作。示例:less agenda.txt

`Head`显示所需文件的前 10 行。示例:head jogos . txt。

`Tail`:显示文件的结尾部分。范例:tail index.html。

`Cat`一次显示所有内容。此命令的缺点是，如果在包含大量内容的文件中使用，则很难找到特定片段。示例:cat routes . go。

### 移动和重命名文件

有趣的是，当我们要移动和重命名某些文件时，我们使用相同的命令。当我们要将文件移动到另一个目录时，我们使用命令`mv nome-do-arquivo caminho/do/destino`。示例:mv dialog.js Documents/backup/

要重命名文件，我们将使用相同的“移动”命令，但我们将使用新文件名，而不是目标目录。

[![Markdowm Image](img/f87bb704050d74b72a46393a4d2f081b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YBNP-RKX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nluryt4ezyla8o9z8y88.gif)

### 显示当前目录

显示当前目录的命令是`pwd`。Pwd 是 Print Work Directory 的缩写，它显示了我们在终端上操作的目录的当前路径。

[![Markdowm Image](img/950ba5d9670938f3f8e0c14059b08dbd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--57-7687l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mn7yupz5ggnmxkwrljil.gif)

### 清洁终端

`Clear`从终端中删除所有使用的命令输出。当我们做很多事情，想要在屏幕上有条不紊地进行的时候，这是非常有用的。

[![Markdowm Image](img/db04429d4c3d8b9e88cbf0e077fb7f27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M5hNVwvG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zyd7xgo1o2hf105l3005.gif)

* * *

### 最后考虑事项

值得一提的是，这只是我们谈论 Shell 脚本时冰山一角。我们将在以后的文章中看到，某些命令在显示或执行其命令的方式上可能会有所不同。

我希望，通过这篇文章，您(读者/读者)对 Shell 脚本的基本功能有了很好的了解，并对这个黑屏失去了一些恐惧。

你可以随意提一些潜在的错误，提出一些建议，还有你认为需要的其他东西。

谢谢你们所有人/