# 将我的博客从 Wordpress 迁移到 Gatsby -第 2 部分

> 原文：<https://dev.to/alanhylands/migrating-my-blog-from-wordpress-to-gatsby-part-2-5ao8>

[![Cover of F. Scott Fitzgerald's The Great Gatsby](img/af90255bba67f91b6dda28d9008c5c6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eY_c3lYV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tmyjr1qhyyu9023n1s70.jpg)

我在第一部分写道，我最终咬紧牙关，给了盖茨比一次机会。在品尝了美味的水之后，我清楚地知道，深入兔子洞只是时间问题。

因此，我花了几周时间将我的主要数据和分析网站[Simple analytics](https://simpleanalytical.com)从 Wordpress 转换成 Gatsby。是的，里面有牦牛毛的成分。

## 与 Wordpress 绝交。

Wordpress 是一个很好的 CMS，所以它应该是——大约整个互联网的 30%运行在它上面。这并不意味着它没有错误，我在这个网站上用的时间越长，它就越让我厌烦。

公平地说，并不是所有这些都是 Wordpress 生态系统的错，事实上，很可能没有几个是。但任何在过去五年里使用过 Wordpress 的人都会告诉你，它与早期那个活泼年轻、有进取心的人相比，几乎已经面目全非了。

我有几个主要问题:

### 1)安全。

由于 mod_security 规则被标记，我的代理主机包不断掉落。不确定就此严格指责 WP 是否公平。当一家历史悠久的托管公司告诉你，他们将不得不完全关闭安全规则，否则你将无法编辑你现有的任何帖子，那么你知道你的软件出了问题。

Wordpress 因其无法在最好的情况下维持足够的安全级别而臭名昭著。任何声称自己从未被黑过的 Wordpress 管理员都只是在游戏中待的时间不够长。这几乎是不可避免的，也是一个必经之路。

我每隔天更新一次插件**。我指的是每天**的**。开发人员快速而定期地修补东西是很好的，但是当它变成一项乏味的日常工作监控并确保更新发生时，是时候重新评估你的方法了。**

正如我上面所说的，当主持人告诉你让你编辑帖子的唯一方法是关闭更多的安全措施时，它发出了警钟。声音大的。

### 2)这么多插件。

Wordpress 生态系统的伟大之处在于插件数量之多。Wordpress 生态系统最糟糕的事情之一就是插件的数量。当我受够了每天的更新时，我已经在我的 WP 系统上运行了 18 个不同的插件来进行简单的分析。

没有人让我使用它们，但我发誓没有一个是无关的。当你不得不安装另一个插件来关闭你最初不想要的新 Gutenberg 编辑器时，是时候重新评估你的人生选择了。你会注意到我在这里经常提到重新评估的问题。

### 3)速度或速度不足。

> 明天我们会跑得更快。

我把《简单分析》转移到《盖茨比》的主要原因可以总结为上面的引用。简而言之，与盖茨比一起建立我的博客网站，并在 Netlify 上托管，就像谚语所说的铲屎一样。对于你们当中的非北爱尔兰人来说，这意味着它相当快。像闪电一样快。尤塞恩·博尔特快。

在 localhost 上进行测试从来都不能公平地代表它在野外的样子，但即使在野外，它看起来也非常好。让它在 Github 和 Netlify 上运行并没有损失任何速度，我非常高兴。

**简单分析是博客。**这是一组文章，给出了我对数据分析师在其数据职业生涯各个阶段遇到的问题、考验和磨难的看法。它是屏幕上的文本和一些图像。就是这样。它不需要完整的数据库和 CMS 支持。盖茨比让我回到那个话题，我非常感激。

## 那么我是如何进行迁移的呢？

我从第一次涉足盖茨比的世界中学到了很多(注:你现在正在读)。移植我的分析博客的一个主要区别是人们真的会阅读它...

我想要一些我没有的东西:

*   顶部导航栏。
*   用于划分主要内容类型的类别(例如文章/采访)
*   标签，用于根据文章或采访类型进行进一步分类。
*   Mailchimp 表单集成。

## 从哪里开始搜索？

我不想从头开始编写整个网站的代码。我的反应和盖茨比的权力还没有完全到位。但是我确实想要一些我可以立即投入使用并且不会丢失任何现有内容的东西。

在从优秀的 [Gatsby starter Library](https://www.gatsbyjs.org/starters/) 的不同 Starter 站点进行了大量**的试错之后，我仍然没有得到我想要的。我转而去了[盖茨比展示区](https://www.gatsbyjs.org/showcase/)，看看网上一些顶级的盖茨比网站是如何组合在一起的。这个展示区展示了 500 多个 Gatsby 站点，其中许多站点都在 Github 上提供了源代码。**

### 好心陌生人的一点点帮助。

manu.ninja 的网站吸引了我的注意。这是前端开发人员[曼努埃尔·威瑟](https://manu.ninja/)的个人博客，为我想要的简单分析钉上了所有的基础。简洁明了的设计和布局。博客。顶部导航栏。提供其他内容页面、标签和类别。

于是我从 Github 下载了 [Manu 的源代码](https://github.com/Lorti/manu.ninja)开始工作。经过大量的修补、砍削、切片、切割和创造，我把马努的原作打造成更属于我自己的东西。如果没有在 Github 上使用所有原始的艰苦工作，我是不可能做到这一点的，尽管这是一个惊人的慷慨行为，有助于促进社区和其他开发人员的学习。谢谢马努威瑟！

### 旧世界遇见新世界。

从 Wordpress 网站移植我自己的内容是一件耗时的事情，但也是一件不可避免的坏事。Peter Akkies 写了一篇关于[将你的 Wordpress 帖子导出到 Gatsby markdown](https://peterakkies.net/export-wordpress-to-gatsby-markdown/) 的精彩博文，给我指出了 [Wordpress-to-Hugo 导出插件](https://github.com/SchumacherFM/wordpress-to-hugo-exporter)的方向。

这个插件节省了大量的复制和手工转换，并且维护了 frontmatter。我仍然不得不手动检查所有的帖子(大约 60 个帖子和页面),并手动纠正任何翻译不正确的降价。彼得在他的文章中警告说，必须修改 slugs 和一些 frontmatter 来适应 Gatsby 博客和 GraphQL，所以这并不令人惊讶。如果我有成百上千的博客文章要转换，情况可能会有所不同。事实上，我做了一点脚本来加速一些命名的改变等等。因为我尝试了一些不同的入门设置，所以掌握一些 Python 或其他脚本语言是很有用的。

### 你有邮件(黑猩猩)。

我必须引入的 manu.ninja 代码中最大的附加部分是 Mailchimp 表单集成。我使用了 [gatsby-plugin-mailchimp](https://www.gatsbyjs.org/packages/gatsby-plugin-mailchimp/) 插件，并配置了一个通用的电子邮件注册表单，以便在这个简单的分析网站上的所有帖子之后包含它。

我将不得不及时重新访问这个网站，因为它只会将人们添加到通用简讯邮件列表中。如果/当我将特定的内容升级和迷你课程添加回网站时，我必须想办法将这些标签添加回 Mailchimp。总是有更多的开发工作要做。

### 还有其他问题吗？

我在 [Wordpress- > Gatsby 第一部分](https://alanhylands.com/migrating-blog-wordpress-gatsby-part-one/)中提到，我不得不做一个电子邮件的变通办法，我已经在这个网站迁移中做了同样的事情。Netlify DNS 非常容易配置，所以添加 A 和 MX 记录指向我的另一个主机提供商的邮件服务器非常简单。

否则，迁移会很快。该网站的页面加载速度非常快，谷歌的 Pagespeed Insights 工具非常受 T2 的喜爱。

如果将来出现任何重大问题，我会向你汇报，但现在我更乐意做出改变。我还没用完 Wordpress。事实上，我花了一周的大部分时间，用 Wordpress 和优秀的 [Amelia](https://wpamelia.com/) 插件为一个客户组装了一个预订系统。

我也不打算成为一名全职的前端开发人员，但这是一次有趣的逗留。从我早期使用 Javascript 到现在，它和网络应用已经有了很大的进步。但是社区中如此多的开发者通过博客帖子、文章和论坛回答提供的支持和指导对快速启动和运行有很大的帮助。我期待着在我已经迁移到盖茨比的这两个[站点](https://simpleanalytical.com)的基础上进行建设。谁知道呢，我甚至可能会抽出时间为他们写更多的文章！