# 什么是默默无闻的安全，为什么它是邪恶的

> 原文：<https://dev.to/ctrlshifti/what-security-through-obscurity-is-and-why-it-s-evil-47d5>

这篇文章解释了什么是通过模糊实现的安全性，为什么它是你唯一的防御手段，以及你可能在你的 web 应用中使用它的一些方法。如果你做了 pentesting，它会给你一些提示，告诉你在 webapps 的什么地方可以找到你可以利用的安全性差的例子。

*Target audience:*

*   *Webapp 开发者，或*
*   *初学五旬节*

### 什么是默默无闻的安全感？

通过模糊实现的安全性依赖于保密性和迷惑攻击者，而不是建立适当的控制来阻止他们。

让我们举一个真实世界的例子。

假设你又回到了青少年时代，你有一个特定的文件夹，里面有你不想让父母发现的文件。你知道那种。

你可能把这个文件夹藏在一大堆其他文件夹后面，并把它命名为无聊的东西。当你知道你的父母没有理由会在“家庭作业”目录中查找时，你可能会感到非常自信。

这将是通过模糊的安全。它可能会工作一段时间，但一旦有人检查 Windows 资源管理器的“常用文件”部分，你的秘密就泄露了。更好的办法是用密码保护你的文件。

在计算中，通过模糊实现的安全性比你想象的更常用。以下是我见过的一些最有害的通过模糊获得安全的例子。

### 对机器人的依赖. txt

Robots.txt 是位于您的域的根目录下的文件，例如`mywebsite.com/robots.txt`。Robots.txt 用于告诉谷歌等搜索引擎不要抓取你网站的某些部分。robots.txt 可能是这样的:

```
User-agent: *
Disallow: /super-secret-passwords/
Disallow: /secret-admin-access-panel/ 
```

所有这些都是为了防止谷歌抓取这些网页！它不能抵御黑客。检查 robots.txt 文件是一个恶意的人可能做的第一件事——当他们看到你告诉谷歌不要抓取“超级秘密密码/”时，你认为他们下一步会去哪里？

相反，如果您希望某个页面不出现在结果中，可以向该页面添加一个 [noindex 元标签](https://support.google.com/webmasters/answer/93710?hl=en)。更好的是，如果除了你之外的任何人都不应该看到页面，请确保它在安全登录页面的后面。如果你不经常走动，也可以考虑 IP 限制。
T3】

### 隐藏常用的 Wordpress 文件

一些 Wordpress 网站试图隐瞒他们运行 Wordpress 的事实。常见的方法包括删除 Wordpress '*readme.html*文件和重命名文件夹，如 *wp-admin* 。

虽然这些可能会吓退一个新手攻击者，但是任何值得上网的黑客都能够通过检查你的 CSS 来判断出你在运行 Wordpress。

另一种选择？说实话，不用太费心了。隐藏你正在使用 Wordpress 的事实没有仅仅保持你的 Wordpress 和插件更新重要。如果你有一个 Wordpress 网站，试着在上面运行 [wpscan](https://github.com/wpscanteam/wpscan) ，看看是否有你应该修复的明显漏洞。
T3】

### 在子域中隐藏不安全的代码

假设你的网站有一部分你想隐藏，可能是一些你仍然需要测试的不安全的代码，或者一些管理控件。你可以做的一个方法是把它存放在一个子域中。

这很好，只要它不是你唯一的安全方法。如果该子域需要安全登录，并且是 IP 受限的，那么你就没事了。但是仅仅将不安全的代码放在没有其他控件的随机子域后面是一个糟糕的想法。[我会在这篇文章](https://www.explainhownow.com/2019/how-to-find-subdomains/)中解释原因。

### 滚动你自己的密码

当然，[考虑到所有其他哈希算法已经存在了多长时间，它们现在肯定是不安全的，对吗？也许自己做比较好。](https://www.google.com/search?client=firefox-b-d&q=lindy+effect)

号码

不不不不。

【T0 号】【T1 号】【T2 号】【T3 号】【T4 号】【T5 号】【T6 号】T7 号

除非你真的知道自己在做什么，否则不要尝试自己编写加密或哈希算法。目前流行的算法已经过安全社区的适当审查，比你自己做的任何东西都安全。如果你自己做算法，它很可能包含你可能会忽略的严重错误。请不要。
T3】

### 依靠模糊的数据库名称

当然，也许将您的“用户”SQL 数据库列重命名为更深奥的名称会使攻击者更难执行 [SQLi](https://www.incapsula.com/web-application-security/sql-injection.html) 。但是如果 SQLi 在你的网站上是可能的，*修复漏洞*应该是你主要关心的。

如果您不确定您的站点是否容易受到 SQLi 的攻击，请查看 [sqlmap](https://github.com/sqlmapproject/sqlmap) 。
T3】

### 秘密参数

这是我的最爱之一，因为 CTF 经常使用它，但它也发生在现实世界中。

假设您有`http://mywebsite.com/normalpage`，但是当您导航到`http://mywebsite.com/normalpage?admin=true`时，管理员访问被启用。

一个普通用户可能不会尝试添加 admin 参数，但是任何一个有 fuzzing 工具的黑客都会在几分钟内找到它。使用秘密参数来控制对隐藏内容的访问是一个坏主意。

当然，“管理”并不是一个秘密的词。如果控制参数是类似于`http://mywebsite.com/normalpage?230j7x9832nnc=true`的东西呢？

现在这和有密码差不多了，对吧？没有。你现在面临的主要问题是，如果你的页面链接到其他任何地方，你的秘密参数有可能显示为 *Referer* heading，如果你只是实现了一个标准的登录面板，这就不是问题了。

如果您*必须*这样做，不管出于什么原因，也许可以考虑将参数移到 POST 请求的主体中。
T3】

### 旁白

当然，晦涩在 webapp 设计中肯定有它的位置。将敏感代码放在子域上或删除对后端的引用是完全合理的。关键的考虑是，这应该*而不是*是你唯一的防线。

通过实现标准的访问控制，并遵循最佳安全实践，您可以确保恰当地使用晦涩。记住:如果你和黑客之间只有默默无闻，那么黑客和你之间只有时间。

如果你对提升你的安全技能感兴趣，看看我在[如何进入渗透测试](https://www.explainhownow.com/2019/learn-penetration-testing/)的另一篇文章。

黑客快乐！

*本帖最初发表于[explainhownow.com](https://www.explainhownow.com/)T3】*