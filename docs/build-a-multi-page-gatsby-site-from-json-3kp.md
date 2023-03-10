# 从 JSON 构建一个多页面的 Gatsby 站点

> 原文：<https://dev.to/ashleemboyer/build-a-multi-page-gatsby-site-from-json-3kp>

当我制作《https://blackbirthdays.com》时，我决定采用这种方法。最初，对于二月，它每天显示不同的人的生日。因此，由于站点的数据非常静态，我决定用一个 JSON 文件将信息输入其中。我想和 Gatsby 一起练习一些东西，所以我开始练习[通过编程从数据中创建页面](https://www.gatsbyjs.org/tutorial/part-seven/)和[使用 GraphQL 获取数据](https://www.gatsbyjs.org/tutorial/part-four/)。

我记不清这样做的每一步，所以我专门为这篇文章做了一个新的网站。别担心，我计划尽快让它变得更好。我希望它最终能成为查看文本颜色/背景颜色对比的有用工具。不管怎样，我们走吧！

### 1。创建您的项目

最简单的一步。:)如果您有任何问题或需要帮助，请访问 [Gatsby 教程](https://www.gatsbyjs.org/docs/quick-start/)。

[![Terminal commands for setting up a new Gatsby project](img/ed2d3ff7630b7ebd6af30ae398e0dc5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jmsq1o3O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d89dd76oml8yy3rsepy6.png)

### 2。创建 JSON 文件

在项目的根目录中放一个新的 JSON 文件。我对数据结构本身有一点疑问，但我认为这需要练习。用数组从 GraphQL 中读取数据更容易，所以尽量把数据转换成那种格式。

[![How I formatted the color data](img/9295e13bf142f4d24706ba3ebc75f2a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IYC2fZ8I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/peuhc2g8ovcrkvoajzj5.png)

### 3。创建模板文件

这个模板文件定义了如何显示所有动态创建的页面。很酷吧。？！你可以在盖茨比的网站上读到更多关于这个的信息。

[![Code for the template file](img/4408f3c6f88f0cacadac623617b34048.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vpIfx4FO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jbcscj813zgm4ekuyeb.png)

### 4。让`gatsby-node.js`文件创建所有页面

这个文件应该已经在您的根目录中，来自步骤 1 中的`gatsby new`命令。这是您实现`createPages` API 函数并告诉站点您在上一步中创建的模板文件的地方。在下面截图的第二行，我指定了步骤 2 中的 JSON 文件。

[![Code for the gatsby-node.js file](img/abbc57c9a11b3a7f0d53f0e87e1edee9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jbjqEG79--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x42s5fslzctbnfrpurs0.png)

### 5。重新启动开发服务器并预览您的站点

在这一点上，一切都应该是好的，你可以开始造型。开始吧！如果你尝试了，请分享你的结果，或者让我知道任何步骤中是否有什么不对劲。尽情享受吧！:)

* * *

你知道我有时事通讯吗？📬

如果你想在我发布新的博客帖子或宣布重大项目时得到通知，请联系 https://ashleemboyer.com/newsletter。