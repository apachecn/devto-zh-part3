# 一天之内建立个人网站的超级简单指南

> 原文：<https://dev.to/campbellbartlett/super-simple-guide-to-building-your-own-personal-website-in-one-day-3k2c>

前几天，在 dev.to 上阅读了许多令人惊叹的简历构建帖子后，我收到了一阵灵感。我打算建造自己的——唯一的问题是:我从哪里开始呢？

我自己不是一个很有创造力的天才，所以我通常把它留给 UI 专业人士。因此，我没有从头开始构建一个 Bootstrap 主题，而是进行了一些谷歌搜索，在那里我偶然发现了一个很好的资源: [Start Bootstrap](http://www.startbootstrap.com) 。

Start Bootstrap 是由贝莱德数字公司的大卫创建的一个很棒的网站。几乎他所有的主题和模板不仅是免费的，看起来很棒，而且使用起来超级简单。

我是这样做的。

**安装**

1.  前往[开始引导](http://www.startbootstrap.com)。
2.  选择一个你喜欢的主题或模板。(我选择了“简历”)
3.  从 Github 中派生或克隆主题，或者甚至下载源代码，如果你喜欢的话，把它放在一个容易访问的文件夹中。
4.  从您喜爱的终端中新创建的文件夹中，运行 npm build。
5.  从同一个文件夹中运行 gulp dev，
6.  尽情享受吧！

如果你一直在家玩游戏，你可能已经注意到你最喜欢的浏览器刚刚打开了一个新标签，上面有你刚刚设置的网站的新副本。但是你如何使它个性化呢？

幸运的是，这也非常简单。

**定制**

1.  自定义文件/scss/_ _ 变量中的颜色。scss -主要和次要颜色是您想在这里更改的。
2.  在 index.html 编辑文本，说出你需要它说的任何内容。
3.  如果你习惯简单的 HTML、CSS 和 Bootstrap，你可以定制菜单、添加图片或改变字体/布局来适应你的风格。
4.  多亏了 gulp dev，当你做出改变时，网站会在你的浏览器中更新。

一个很好的图片资源是 [undraw](https://undraw.co/illustrations) ,特别是当你可以从你的自举主题中配色你的原色。

**部署**(免费！)

1.  如果你还没有，创建一个 Github 帐户
2.  创建一个名为 username.github.io *的新存储库，用户名必须与您的 github 帐户名完全匹配！*
3.  将您正在使用的文件夹的内容提交到您新创建的存储库中。
4.  前往 *yourname* .github.io 查看你的新网站

关于 Github 页面的更多信息，请点击这里查看文档

这就是全部了！

你可以在[Campbell bartlett . github . io](http://campbellbartlett.github.io)看到我的网站——让我知道你的想法。如果你觉得可以改进，请告诉我；我欢迎所有的贡献。

我希望这能对你有所帮助。