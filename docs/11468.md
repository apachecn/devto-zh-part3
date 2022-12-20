# ExpressionEngine 中的调试

> 原文：<https://dev.to/eecms/debugging-in-expressionengine-1d4l>

2017 年，我在丹佛的 EECONF 上做了一个关于 PHP 分步调试的[演示，特别是在](https://speakerdeck.com/boldminded/stop-using-var-dump-embrace-step-debugging) [ExpressionEngine](https://expressionengine.com) CMS 中，然而，我没有深入讨论模板代码中的调试问题。与其他基于 PHP 的内容管理系统相比，ExpressionEngine 有点与众不同。它有一个非常容易学习的模板语言，但它也有点细致入微。模板是基于字符串的，有多个解析步骤。 [Low](https://expressionengine.stackexchange.com/users/172/low) 在这个 [StackExchange](https://expressionengine.stackexchange.com/questions/6/what-is-parse-order-and-how-does-it-affect-how-my-template-is-coded-rendered) 线程中很好地解释了解析过程。由于模板是解析的，所以您需要调试解析模板的 PHP，而不是直接调试模板代码。

调试模板代码是我自己开发 ExpressionEngine 网站或者帮助客户调试我的一个附加组件时经常做的事情。模板标签可能只是没有输出它们期望的内容，而不是得到特定的错误消息。

偶尔我会请他们分享他们正在使用的模板代码，我会得到一个包含几百行代码的*完整的*模板文件。对我来说，这表明他们不知道从哪里开始调试问题。例如，我的 [Publisher](https://boldminded.com/add-ons/publisher) 插件非常复杂，它的主要功能之一就是在`{exp:channel:entries}`标签中显示翻译后的内容。为此，它在 ExpressionEngine 中使用了`channel_entry_query_results`钩子。一个页面上有多个`{exp:channel:entries}`标签是很常见的，每个标签都调用`channel_entry_query_results`钩子。这意味着我需要调试的代码被多次调用。

当我不得不 FTP 到某人的站点并自己调试时，这尤其是一个问题。我不能远程单步调试(我可以，但是这需要修改服务器配置，我不能要求客户这么做)，所以我通常求助于`var_dump();`或者写入本地日志文件。如果问题存在于单个`{exp:channel:entries}`标签中，但是页面上有另外两个`{exp:channel:entries}`标签，那么我正在调试的代码会被额外调用两次，这两次都可能与我正在调试的问题无关。想象一下玩*让我们做个交易*，你有三扇门，你必须选择正确的门来赢得奖品，在这种情况下，奖品是臭虫。相信我，这不是一个开发者想玩的游戏。花时间选择和冒险进入错误的门是浪费时间，而且常常令人沮丧。那些额外的钩子调用只会碍事，增加噪音。作为一名开发人员，我想尽可能快地避免噪音和隔离问题。

## 解决方法:**简化**

从等式中移除另外两扇门。从模板中删除与你试图解决的问题没有直接关系的所有内容。这意味着移除页眉、页脚、导航，甚至可能移除所有 JavaScript 和 CSS 代码。把它分解成复制 bug 所必需的东西。

如果不知道哪个模板代码是问题子，怎么办？开始移除大块的代码，直到您可以隔离出哪个区域，或者模板标签被明确地标识为导致问题的区域。

这是我最近收到的一张支持票的一个使用案例。客户有 3 个渠道:页面、出版物和文档。该页面条目使用一个 [Bloqs](https://eebloqs.com) 字段来显示来自 Publications channel 的一个条目，该条目本身有一个与 Documents channel 中另一个条目的关系字段。然后文档频道有一个包含 PDF 的自定义文件字段。他们还使用 Publisher 以两种不同语言的翻译内容显示所有这些内容。这种情况下的错误是文档条目中的文件字段总是显示默认的语言值`english.pdf`。我首先尝试用他们提供的模板代码来调试这个问题:

```
{exp:channel:entries entry_id="3"}
    <h1>{title}</h1>
    {bloqs_field}
        {related_publication_bloq}
            <h2>{title}</h2>
            {related_document}
                <h3>{related_document:title}</h3>
                file: {related_document:file}
            {/related_document}
        {/related_publication_bloq}
    {/bloqs_field}
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

注意，提供的代码已经非常简单了。在我的要求下，客户花时间建立了一个全新的 ExpressionEngine 环境，其中只有重现问题所需的必需品。他消除了噪音。这对我非常有帮助，也是我经常问客户的问题。

经过一点调试(他们给我发了一份测试站点的完整副本，让我在本地运行)之后，我仍然不确定问题的原因是什么。我花了将近一个小时试图弄明白。为了简化这个问题，我把上面的模板标签分成 3 个独立的`{exp:channel:entries}`标签。毕竟每个`{related_`字段只是调用另一个入口。所以我把模板分解成下面的代码，它本质上做的和前面的代码片段完全一样，但是在 3 个独立的`{exp:channel:entries}`标签中。

```
<!-- Page -->
{exp:channel:entries entry_id="3"}
    <h1>{title}</h1>
{/exp:channel:entries}

<!-- Publication -->
{exp:channel:entries entry_id="2"}
    <h2>{title}</h2>
    {related_document}
        <h3>{related_document:title}</h3>
        file: {related_document:file}
    {/related_document}
{/exp:channel:entries}

<!-- Document -->
{exp:channel:entries entry_id="1"}
    <h2>{title}</h2>
    file: {file}
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

通过页面上的所有三个条目，我可以立即看出哪个有效，哪个无效，并且很明显`{file}`字段没有打印出正确的值。然后我注释掉了前两个`{exp:channel:entries}`标签，并单步调试了第三个标签。因为它是页面上唯一没有`{related_`字段的`{exp:channel:entries}`标签，所以我不必处理额外的调试噪音，几分钟后就找到了问题的根源。即使您没有单步调试，简化模板也能让您专注于问题领域，从而更快地找到解决方案。
—

最初发表于 u.expressionengine.com[的](http://u.expressionengine.com/article/debugging-in-expressionengine) [Brian Litzinger](https://u.expressionengine.com/author/brian-litzinger)