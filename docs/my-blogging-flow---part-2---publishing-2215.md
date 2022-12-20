# 我的博客流-第 2 部分-出版

> 原文：<https://dev.to/dmerejkowsky/my-blogging-flow---part-2---publishing-2215>

*原载于[我的博客](https://dmerej.info/blog/post/my-blogging-flow-part-2-publishing/)T3。*

欢迎来到“博客是如何写出来的”的第二部分。如果你还没有，请随意先阅读[第 1 部分【t 1:)](https://dmerej.info/blog/post/my-blogging-flow-part-1-under-the-hood/)

# 想法

一切从一个想法开始。我在自己的专用机器上有一个小的 git 存储库，其中包含一堆 markdown 文件，每篇文章一个。(顺便说一下，这个存储库是私有的)。

这些文件的内容可以从几行到几乎完整的文章不等。大多数时候，它们只包含一个基本的轮廓。

一有想法，我就更新`ideas`库。尽快把事情写下来很重要。忘记你想说什么或你想怎么说的发生得比你想象的要快得多。

# 初稿

接下来，是时候开始写全文了。我有一个小的 Python 脚本:

*   找出下一篇文章的编号，并为我填充前面的内容(因为我很懒)
*   对生成的 Markdown 文件运行`git add`，这样我就不会因为运行`git clean`而误删除它(因为会出错)
*   在我的文本编辑器中打开降价文件

现在该起草文章了。我已经配置了我的编辑器，以便:

*   拼写检查已激活
*   语法高亮被禁用(我已经在之前的博客文章中解释了为什么[)。](https://dmerej.info/blog/post/syntax-highlighting-is-useless/)

在另一个终端(通常隐藏在标签后面)，我运行了`hugo serve`，最后，在文本编辑器旁边，是一个 web 浏览器，这样我就可以实时看到发生的变化。

# 校对

然后就该校对了，校对，校对…

我通常会等睡个好觉再重读一篇文章。

如果文章真的很大很复杂，我有时会在 GitHub 上打开一个 pull 请求，让朋友或同事审阅。

我在我的文本编辑器和浏览器中校对，以增加我看到拼写错误、笨拙的表达、过渡问题等的机会。

# 出版

我有另一个 Python 脚本来自动发布。它是这样做的:

*   自动提交以防我忘记(如果存储库是干净的，脚本会跳过这一部分)
*   运行`hugo --buildDrafts=false`来构建 HTML 文件
*   运行`rsync`将它们上传到我的服务器
*   运行`git push`将更改发布到 GitHub

请注意，新帖子的发布会触发博客提要的新版本[。](https://dmerej.info/blog/index.xml)

这个脚本可以在对已经发表的文章进行任何更正后运行。

当一篇全新的文章刚刚发表时，我运行另一个 Python 脚本来:

*   解析 RSS 提要并找出最新文章的 URL 和标题
*   写一条类似于[这条](https://twitter.com/d_merej/status/1092029859864416259)的自动 tweet(末尾的附加标签在命令行中给出)
*   在乳齿象上写一个类似内容的嘟嘟
*   向订阅了[简讯](https://dev.to/tanker/introducing-dmerejs-newsletter-42c-temp-slug-7813984)的人发送电子邮件。

# 开发到

然后是等待的时间，直到`dev.to`获得最新版本的 RSS 提要，从提要中解析 HTML，将其转换回 markdown，并将其添加到我的仪表板中。

(是的，你可以配置`dev.to`来做这件事！)

考虑到 HTML 中怪异的往返过程，这种方式非常有效。不过，为了以防万一，我必须重新阅读 dev.to importer 创建的 markdown。这给了我最后一次校对文章的机会。

# 反馈

多亏了 isso，当有新的评论发布时，我会得到通知。我当然也会收到来自 dev.to 的通知。

当我幸运的时候， [@thepracticaldev](https://twitter.com/thepracticaldev) 会发一个链接到我的文章，我会有新的关注者和更多的浏览量。这个不错:)

那都是乡亲们！如果你有任何关于博客如何运作的问题，不要害怕在下面的评论中提问！

* * *

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。