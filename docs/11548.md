# 面向视觉倾向者的 PostgreSQL 工具

> 原文：<https://dev.to/azure/postgresql-tools-for-the-visually-inclined-2b8b>

我的职业生涯始于使用拖放工具构建表单和网站的微软堆栈。随着时间的推移，这成为一个笑点，这是不幸的，因为老实说，生产力是疯狂的。

2008 年，我跳到了 Linux 世界，完全迷失了方向。一切都是该死的文本文件。是的，你可以使用 Mac 或 Ubuntu 或任何当前看起来有趣的 Unix 桌面，但是根本不需要知道你的命令，我就是这样做的。

就像学习 SQL 一样，学习你的文本命令会让你更有效率。*我向你保证我不会翻转 l33t 位*。我在这里不是为了说服任何人——我想做的是分享我是如何接受 PostgreSQL 命令行的，并为此感到非常高兴。

## 友好对友好

多年来我一直想写这篇文章，但是是 Ryan Booz 的这篇文章让我对编辑产生了兴趣。Ryan 是一名 SQL Server DBA，他正在撰写一个系列文章，讲述他在 15 年(！)SQL Server DBA 职业生涯。我无法想象改变是一件容易的事。

基本上，Ryan 有顾虑(我理解):

> 在 PostgreSQL 的例子中，我很快得出结论，糟糕的工具是 SQL Server 社区理解起来如此困难和复杂的主要原因之一。即使是我目前工作的对数据库没有任何特殊兴趣的开发人员也认识到 PostgreSQL 感觉更像是一个黑匣子，而不是他们以前使用 SQL Server 的有限经验。

我不能说他在这一点上错了，尽管我要说“坏”这个词有点主观。

让我开门见山地说:从 SQL Server 跳到 PostgreSQL 比更换工具要*得多*。 **PostgreSQL 建立在 Unix** 之上，考虑到 Unix 是首选平台，通常当它运行在某种类型的 Unix 机器上时运行得最好。**Unix 世界有一个非常具体的习语**来指导如何做事，它**当然不是可视化的**！

作为一个学习可视化编程的人，我必须了解每个图标的含义以及在哪里发生了什么的视觉提示。我开始理解属性的痛苦，按钮文本下描述快捷方式的线条，以及每个表单惯用的布局。执行命令意味着按下按钮。

在 Unix 世界中，您写出这个命令。复选框和对话框被替换为选项标志和参数。你安装你需要的工具，然后寻找帮助你做一件事的二进制文件，然后你询问他们的帮助，通常使用一个`--help`命令(或者只是简单的`help`)。

PostgreSQL 也是如此。我认为这是困扰 Ryan 的问题。他在一个拥抱完全不同的习语的世界中寻找视觉工具。这就像去了巴黎，却不喜欢它(和法国)，因为那里的烧烤太可怕了。

让我们通过一些常见的 PostgreSQL DBA“东西”来展示我的意思。

## [T1】你最好的朋友:psql](#your-best-friend-psql)

当你第一次遇到一个新的 Unix 工具时(是的，我是在给 PostgreSQL 贴标签),你会想出这个工具的二进制文件。PostgreSQL 有很多您想了解的，包括`pg_dump`和`pg_restore`等等。我们现在想要的是`psql`，它是与服务器一起安装的 PostgreSQL 交互终端。让我们打开它，问问它到底是怎么回事:

<figure>[![](img/66e5ec5d7cf4035077defd56da7a6f0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qPMaYyqC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1430.png%3Ffit%3D1024%252C731%26ssl%3D1) 

<figcaption>你好 psql</figcaption>

</figure>

我使用的是 Mac 的终端应用程序，但你可以使用任何你喜欢的 shell，包括 Powershell 和 Windows 命令行。不过，我强烈建议您打开 Linux VM 或 Docker，体验一下使用 PostgreSQL 的“滋味”。事实上，你可以在巴黎找到烧烤，但探索当地美食可能会有所帮助。

第一次通读这个选项和命令列表需要一些耐心，但是值得！列表顶部是常见选项，比如使用`-c`运行命令，使用`-d`运行命令的数据库。然而，在这个帮助屏幕的最后有一个关键陈述:

<figure>[![](img/f6fa3552ebecc0250f1bc362e242d2c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RRC35B-O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1432.png%3Ffit%3D1024%252C731%26ssl%3D1) 

<figcaption>psql:是互动的！</figcaption>

</figure>

`psql`工具是交互式的！这将有助于我们——所以让我们登录数据库，四处看看。但是哪个数据库呢？我们将通过在命令行上运行以下命令来创建一个:

```
createdb redfour 
```

`createdb`二进制文件有一个任务，以典型的 Unix 方式:*在本地服务器*上创建一个数据库。它也有一个对应的二进制:`dropdb`。我怎么知道这个？这是您在使用 Unix 系统时所习惯的事情之一——弄清楚二进制文件以及它们的作用。

你是怎么做到的？到目前为止，我们只知道一个二进制文件，`psql`，所以让我们看看它在哪里，希望其他二进制文件也在那里:

<figure>[![](img/e7c0b1eb5216053a8117c40778321c51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4T1gcQjw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1433.png%3Ffit%3D1024%252C561%26ssl%3D1) 

<figcaption>用哪个和 ls 来告诉我们更多</figcaption>

</figure>

这是你随着时间的推移学到的事情之一:询问它正在使用的工具/二进制/运行时的 Unix `which`版本以及它在哪里。该命令的结果告诉我,`psql`位于`/Applications/.../bin`目录中，这对于二进制工具来说是相当标准的。我将结果复制/粘贴到`ls`命令(列表内容),我们可以看到由我们支配的二进制工具。

耶。让我们登录并四处游玩。

[![](img/7ac471ea354e8ea6e0876a2b93366040.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7oS7S-ZM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1436.png%3Ffit%3D1024%252C360%26ssl%3D1)

## 到底发生了什么？

现在，我在数据库中的一个交互式终端前，不知道下一步该做什么。这是可视化工具的主要优点:您可以根据提示来了解正在发生的事情。这就是 Xbox 上的 Halo 和一个老派 MUD 的区别——感觉过时又傻。

让我们继续下去，看看这是不是真的。当我们之前运行`--help`命令时，它告诉我们使用`\?`来找出 psql 中的命令，所以让我们先试试:

<figure>[![](img/6caf85045944a0b5892e1f4d068a06a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CLJ2aYQk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1437.png%3Ffit%3D1024%252C543%26ssl%3D1) 

<figcaption>你好文字之海轰然笼罩我！</figcaption>

</figure>

这里有太多的东西需要吸收。所有这些神秘的小命令*做一些事情*，但是他们做什么，起初，你可能不明白。这又是一个耐心的深呼吸点，因为很快我们就要点燃这狗屎了(以一种好的方式)。你所拥有的，就在这里，有许多原始的*力量*就在你的指尖。只需要几个小时就可以理解这些命令的节奏以及它们的修饰符。我将在几分钟内向你展示我的确切意思，现在让我们先把自己接地。

向下滚动(使用向下箭头或鼠标)到信息命令部分。这是你的面包和黄油——在这里你可以快速浏览你的数据库。我们可以通过使用`\d`(按 Q 退出帮助页面的文本视图)来做到这一点:

[![](img/78c52c0ac01e3c6dfdbc43ff8760a2eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gRVDn8ll--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1438.png%3Ffit%3D1024%252C536%26ssl%3D1)

我们的数据库是空的。让我们通过为用户创建一个快速表格来解决这个问题:

[![](img/ea364549c661c611348a2d010adac607.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5QB_nsuT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1439.png%3Ffit%3D1024%252C539%26ssl%3D1)

当您在 psql 中编写 SQL 命令时，您可以换行。请注意，提示也发生了变化，告诉您当前有一个开放的 paren。为了结束这个命令，添加一个分号，我们就完成了。*注意:我不会进入 SQL，但[它真的值得你花时间](http://www.craigkerstiens.com/2019/02/12/sql-most-valuable-skill/)去学习。*

现在让我们再次列出我们的关系:

[![](img/00b2518c1eccbb53832dfbb3dbdd2597.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gQb6elCj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1440.png%3Ffit%3D1024%252C539%26ssl%3D1)

很可爱。我们有自己的表和处理该表的 id 生成的东西，称为*序列*。让我们使用`\d users`询问 psql 更多关于这个表的信息:

[![](img/0710f6b764f906ec43cfa03d502a9f1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zT2MiyGQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1441.png%3Ffit%3D1024%252C422%26ssl%3D1)

我们桌子的结构是用辉煌的 ASCII 码设计的，充满了信息，完全没有任何漂亮的东西。对于视觉型的人来说，这是一个关闭，因为这与他们习惯的完全不同(我理解)。对于习惯在基于文本的习语工作的人来说，这简直是天赐良机。

为什么？*是东西的速度*。让我们解决这个问题吧。您的一个应用程序开发人员刚刚对他们的 ORM 做了一些不明智的事情，他们认为他们可能破坏了`users`表。你决定调查:

```
psql redfour
\dt users 
```

当您刚刚开始使用 PostgreSQL(和 psql)时，您需要为检查表的命令挤出一点内存。过一会儿，在你下一次呼吸之前，你的手指会完成打字。

*这就是你作为 DBA* 想要的权力。

此时，我可以使用所有可用的 psql 命令，但是我鼓励您自己探索这些命令，看看有什么可能，以及您能以多快的速度完成它。我的同事(现在说起来很有趣)Craig Kerstiens 写了大量关于 PostgreSQL 的文章，这篇文章对习惯了命令行的人非常有帮助。

我想知道为什么这种事情很重要。

## 文字是一种很厉害的药物

如果我让你使用你最喜欢的可视化工具将数据从一台服务器转移到另一台服务器，你会怎么做？如果你经常这样做，那么这个过程将会很简单，可能包括一些右键单击、遍历菜单，以及在你选择的工具中启动一个过程。

在 Unix 领域(因此也是 Postgres 领域)，记住一些命令是很重要的。但这正是有趣的地方，因为 Unix 中的一切都是文本文件。在 Unix 中，你能想到的几乎所有任务都可以使用基于文本的命令来完成。当所有的建筑都是用肉做的，塞纳河是一条火炭河的时候，这就像在巴黎找烧烤一样。

为了向您展示我的意思，下面是如何将您的生产数据库下载到您的本地服务器:

```
pg_dump postgres://user:password@server/db > redfour.sql
createdb redfour
psql redfour < redfour.sql 
```

`pg_dump`二进制文件的唯一任务是将数据库转换成 SQL 文件。当然，你可以调整和配置这是如何工作的，并找出你会使用的所有选项…你能猜到吗？`pg_dump --help`。因此，我们将结构和数据转储到一个 SQL 文件，创建我们的数据库，然后将该 SQL 文件作为一个命令推入我们的新数据库。

在一个较小的数据库(约 20mb)上，整个过程将在不到 5 秒的时间内执行。这就是为什么我们喜欢文本和基于文本的界面——速度！

## 总有办法

正如你可能会告诉，我有这样的对话不止几次。可以肯定的是，视觉效果非常重要！但是当涉及到作为 DBA 的日常工作流时，它们有自己的位置。我认为双击、右键单击和拖放比花时间记忆一些常用命令要慢得多。

然而，psql 最糟糕的地方是视觉效果。在大型表上执行查询看起来很可怕:

<figure>[![](img/b102d27c925ce65016311737c7d6d33c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ctDl2W2---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1443.png%3Ffit%3D1024%252C667%26ssl%3D1)

<figcaption>yucky Yuck</figcaption>

</figure>

这是 DVD 租赁示例数据库，运行一个`select * from film;`查询。看起来像垃圾！好消息是我们*应该*能够解决这个问题。我们来问问 psql 使用`\?`是怎么回事:

[![](img/972aed26aeb6ca434f27e9e9f3655e96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gSgrjhdR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1442.png%3Ffit%3D1024%252C575%26ssl%3D1)

这里有两件事需要注意。第一个是`\x`，它允许扩展输出，或数据的垂直对齐。看起来像这样:

<figure>[![](img/76659953656052dbb35ea60c37443164.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7JXyYcr3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1444.png%3Ffit%3D1024%252C667%26ssl%3D1) 

<figcaption>使用扩展输出</figcaption>

</figure>

您可以做的另一件事是使用`\H`将 HTML 设置为输出。这将执行查询，返回 HTML 格式的结果:

[![](img/ae7e861c7c88a07ef0f074708de70280.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YCXw6Ar1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1445.png%3Ffit%3D1024%252C667%26ssl%3D1)

这很有趣，但我想把它保存到一个文件中。为此，我可以使用`\o`(可以在帮助菜单中看到)并指定哪个文件:

[![](img/698ad5617e01837da14681f9e2a6dfc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M5fa22YR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1448-1.png%3Ffit%3D1024%252C525%26ssl%3D1)

生成的文件并不十分令人兴奋，但还是有些用处的:

[![](img/480e0226996780d14cacb17ad45c42df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xCcX8I4r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1449.png%3Ffit%3D1024%252C648%26ssl%3D1)

在这里，我们可以拥抱 Unix 的文本本质，并看看如果我们开始将二进制文件与一些核心 Unix 命令(都是基于文本的)放在一起会发生什么。

让我们使用 psql 来执行一个查询，但是这次我们将使用 Bootstrap 来格式化:

```
echo "<link rel='stylesheet' href='https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css'>" > report.html && psql chinook -c "select * from film" -H >> report.html && open report.html 
```

[![](img/6b9196bc7db05e657bceee2b254b0ec1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F6wF_CxE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rob.conery.io/wp-content/uploads/2019/03/screenshot_1450.png%3Ffit%3D1024%252C608%26ssl%3D1)

好吧，这当然不是疯狂的惊人，但对于一个快速的报告来说，也不是那么糟糕。您可以修改 SQL 语句，只输出您想要的列，并且可以使用 bash 函数将调用形式化，使其更加美观。

## 是啊但这不是管理工作室！

非常正确。例如，您不能双击表格并编辑行，也没有 spiff 图标。更改数据是通过插入和更新命令完成的，删除是通过删除完成的。毫无疑问，这是你必须习惯的事情，如果这是你的一个常见任务，那么你可能希望专注于一个允许这样做的工具(比如免费的 [Postico](https://eggerapps.at/postico/) )。

如果有一个使用 psql 的理由，那就是速度。我也会争论*动力*，但是现在我认为速度是主要原因。在您知道发生了什么之前，您就已经完成了，如果您有重复的任务，您可以将您的命令保存为文本文件，以便在您需要时运行。

改变并不容易，但是我认识的做出改变的人每天都在使用 psql，并且非常喜欢它。他们也可以根据需要转换成可视化工具。然而，他们都同意的一件事是，他们一点也不怀念视觉上的东西。