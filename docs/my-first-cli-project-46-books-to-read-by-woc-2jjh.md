# 我的第一个 CLI 项目——WOC 的 46 本书

> 原文：<https://dev.to/emiko/my-first-cli-project-46-books-to-read-by-woc-2jjh>

# 我的第一个 CLI 项目——2018 年 WOC 要读的 46 本书

在这个项目开始时，学生被要求填写一个项目构思表，并提交审查。当我填写表格时，我有所有这些伟大的想法，我想让我的应用程序做所有的事情(按书列出，按作者列出，按书将发行的月份列出，按评级列出)，但当我得到关于我的项目想法的反馈时，它建议我保持简单(或[KISS](http://blog.flatironschool.com/5-steps-finishing-coding-project//)——编程中的设计原则之一)。我很高兴我听了这个反馈。

我通过观看 Avi 的精彩视频开始了我的项目，该视频将带您了解如何使用 bundler 创建 gem(键入:bundle gem CLI _ app _ name _ of _ project)。我强烈推荐你在开始你的项目之前看这个视频。通过制作一个 CLI Gem 项目，你可以看到 Avi 是如何思考的，我用它作为一个参考框架来开始我自己的项目。

在我使用 gem bundler 建立了我的项目框架之后，我设置了我的环境以便我的 pry，nokigiri，open uri 可以工作。我用纸和笔画出了一个流程图，描述了我希望用户看到的东西，从而开始了这个项目。看起来是这样的(但是有更多的删除线和曲线):

问候->书籍列表->请求输入(你想多看哪本书？)->列出更多信息->返回列表/选择另一本书->退出。

然后，我就动手了！我通过编写我希望拥有的代码开始编码(Avi 在他的 CLI Gem 漫游视频中多次提到)。我从编写 CLI 类开始。我编写了一个问候方法，然后为 list_books 编写了一个 outline 方法，等等，这样我就有了一个框架。这是我项目的[演练](https://www.youtube.com/watch?v=mJL5HqNb2xg&feature=youtu.be)！

这个项目真的促使我思考一个项目从开始到完成的全过程，一些我还没有做过的事情，哇，我真的学到了很多！