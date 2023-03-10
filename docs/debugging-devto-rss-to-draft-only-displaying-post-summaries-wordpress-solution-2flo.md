# 调试:开发。只显示文章摘要(WordPress 解决方案)

> 原文：<https://dev.to/stephencweiss/debugging-devto-rss-to-draft-only-displaying-post-summaries-wordpress-solution-2flo>

# 前置

我把它归入“我为自己创造的问题，我不想忘记我是如何解决的。”还有，我不想成为`DenverCoder9`。

<figure>[![Relevant XKCD: Don't be DenverCoder9](img/e50f16539d21403d4bb12f998709e4d5.png)](https://xkcd.com/979/) 

<figcaption>不要当 DenverCoder9</figcaption>

</figure>

(来源: [XKCD](https://xkcd.com/979/) )

# 仅显示过帐汇总的开发至草稿

可以订阅你博客的 RSS 源，这使得交叉发布你的文章变得容易。

然而，当我第一次建立我的博客时，我注意到我并没有得到完整的帖子，而只是一个简介。

[![Dev.To Draft showing only a summary of my post](img/03d43a4b0a91e99d7a09902a40e47cb5.png)](https://i1.wp.com/www.stephencharlesweiss.com/wp-content/uploads/2018/12/devto-screengrab.png?ssl=1)

尽管我很想把矛头指向别处，但问题出在我的设置上。

我的主网站是建立在 WordPress 上的，我的 feed 被设置为只分享摘要。幸运的是，这是一个简单的修复方法(WordPress 在这里浏览了[和](https://en.support.wordpress.com/settings/reading-settings/))。

让我们来看看这些步骤。

# 更新您的 RSS 源设置

WordPress 使改变你的 RSS 订阅设置成为一个简单的三步过程。

1.  进入管理面板的阅读设置(仪表板>设置>阅读)`https://www.<your-blog-here>.com/wp-admin/options-reading.php`
2.  将设置“为订阅源中的每篇文章显示”从“摘要”更改为“全文” [![Wordpress RSS Feed Settings](img/d48ee707f4ebd09ac10507836b31eab6.png)](https://i1.wp.com/www.stephencharlesweiss.com/wp-content/uploads/2018/12/wordpress-settings.png?ssl=1)
3.  保存更改

# 刷新你的`Dev.to`草稿

现在你已经更新了 WordPress，我们需要返回到`Dev.to`并重置提要。

1.  在[https://dev.to/settings/publishing-from-rss](https://dev.to/settings/publishing-from-rss)返回到`Dev.to`的设置
2.  点击从 RSS 发布
3.  (重新)提交并等待。>每次您提交此表单时，系统都会获取您的订阅源，并且此后会定期自动获取更新。

这个过程可能需要几分钟，请耐心等待。

(我确实浏览并删除了所有的旧草稿，尽管我不知道这是否有助于/有损于这个过程。)

感谢阅读！希望这能帮助你避免我遇到的同样的问题！