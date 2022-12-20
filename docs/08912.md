# 友好的浏览器

> 原文：<https://dev.to/glennmen/friendly-browsers-gf5>

这是一个关于浏览器如何帮助我们网络开发者的小帖子。它不会包含任何深入的信息，但更一般的方法。也许在以后的博客文章中，我会详细介绍。此外，这篇文章将集中在 HTML 错误。

即使我们拥有强大的 IDE，内置林挺和警告消息，所有的外部林挺检查都可以通过预提交钩子、Github 提交触发器，...我们开发者有无尽的选择，但我们仍然会犯错。

我提到的错误类型也存在于开发项目中，我认为大多数错误在过去几周内已经得到了修复。

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 评论为 #1828](https://github.com/thepracticaldev/dev.to/issues/1828#issuecomment-465199995) 

[![Glennmen avatar](img/ec99364605f14852f2b615f6b6dfa0e6.png)](https://github.com/Glennmen) **[Glennmen](https://github.com/Glennmen)** commented on [<time datetime="2019-02-19T16:17:23Z">Feb 19, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1828#issuecomment-465199995)

我已经计划就我在这个问题之前发现的其他东西发表一个问题，但因为它有点相关，我将把它添加到这里的讨论中(如果需要，我仍然可以单独发表一个问题)。这是承诺前林挺、特拉维斯、codeclimate 没有注意到的，...但我只是因为我的 IDE (RubyMine/JetBrains)才注意到的。可能没有一个用户注意到，因为幸运的是浏览器非常“宽容”,即使有错误也能解析 HTML。

除了现在抛出的林挺错误之外，视图文件加载中还有大量的 HTML 错误，例如:不同的开始和结束标记:[https://github . com/thepractical dev/dev . to/blob/e 676d 9 fc 42 CB 6 acdb 3476 ABC 4b 7074 CDA 676 edaf/app/views/pages/_ publishing _ from _ RSS _ guide _ text . HTML . erb # L6](https://github.com/thepracticaldev/dev.to/blob/e676d9fc42cb6acdb3476abc4b7074cda676edaf/app/views/pages/_publishing_from_rss_guide_text.html.erb#L6)

无`<dl>`结束标签(同一文件中多次):[https://github . com/thepractical dev/dev . to/blob/e 676d 9 fc 42 CB 6 acdb 3476 ABC 4b 7074 CDA 676 edaf/app/views/pages/_ editor _ guide _ text . html . erb # L173-L178](https://github.com/thepracticaldev/dev.to/blob/e676d9fc42cb6acdb3476abc4b7074cda676edaf/app/views/pages/_editor_guide_text.html.erb#L173-L178)

没有`<h4>`结束标签:[https://github . com/thepractical dev/dev . to/blob/e 676d 9 fc 42 CB 6 acdb 3476 ABC 4b 7074 CDA 676 edaf/app/views/pages/generator . html . erb # L42](https://github.com/thepracticaldev/dev.to/blob/e676d9fc42cb6acdb3476abc4b7074cda676edaf/app/views/pages/generator.html.erb#L42)

我想你明白了<g-emoji class="g-emoji" alias="wink" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f609.png">😉</g-emoji>我想浏览一下`/app/view/*`中的文件，并修复任何 HTML 错误(以及林挺错误)。但是首先需要知道几件事:

*   如何提交？
    *   1 大公关
    *   每个文件夹的独立 PR(由第一个子文件夹、PR 中包含的嵌套文件夹组合)
*   我无法修复的林挺错误？
    *   忽略他们
    *   通知 DEV 的人？更改 lint 规则并将主服务器合并到我的分支以提交而不出错。

目前这些是我唯一能想到的问题。

你们觉得怎么样？

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1828#issuecomment-465199995)

这些都是很小的错误，很容易被忽略，但它们仍然是应该被修复的有效错误。

因此，即使这些错误在开发过程中溜走，得到审查者的批准，甚至可能通过林挺工具(可能没有正确配置)，浏览器在大多数情况下仍然可以正确解析它。它会在它认为应该添加标签的地方添加丢失的标签，但这里的问题是，这可能因每个浏览器而异，因此可能会导致不必要的行为。

一个很小的例子。我制作了一个包含两个输入字段的 JSFiddle，一个包含一个 div 容器，另一个只包含一个开始的 div。结果被正确解析，用户将看到两个输入字段。

[https://jsfiddle.net/bhondtaw/1/embedded/html,result//dark](https://jsfiddle.net/bhondtaw/1/embedded/html,result//dark)

在我的浏览器中检查解析后的源代码，发现第二个 div 的解析有点奇怪。

```
<div>
  <input id="test" placeholder="unclosed div">

  <script>
    // tell the embed parent frame the height of the content
    if (window.parent && window.parent.parent){
      window.parent.parent.postMessage(["resultsFrame", {
        height: document.body.getBoundingClientRect().height,
        slug: "bhondtaw"
      }], "*")
    }

    // always overwrite window.name, in case users try to set it manually
    window.name = "result"
  </script>

</div> 
```

它在 script 标签之后添加了结束的 div 标签(由 JSFiddle 添加)，而如果您正确地关闭了 div，script 标签将被添加到 div 容器之后。
*我在 macOS 上使用 Brave 浏览器，因此这可能会因您使用的浏览器而有所不同。这里有一个堆栈溢出的例子，它在不同的浏览器中给出了不同的结果: [CSS，HTML Web 浏览器无效标记容限](https://stackoverflow.com/questions/4254216/css-html-web-browser-invalid-markup-tolerances)*

在我的例子中，这不会造成任何伤害，但是依靠浏览器来“修复”你的错误可能并不是在所有情况下都有效。

**实际上对于结构不正确的 HTML 代码有一个术语:[标签汤](https://en.wikipedia.org/wiki/Tag_soup)。**

我认为我们作为开发人员需要保持一定的质量水平，不要害怕花时间审查你的代码。在我看来，这不是浪费时间，我们无法预测代码在每个浏览器中的行为，应该避免浏览器需要纠正我们的错误。稍后必须进行调试可能会花费你更多的时间来找出问题所在，尤其是如果你不知道客户端使用的是什么浏览器(和浏览器版本)。

就像我在一开始说的，有很多工具可以帮助我们开发者完成这项任务，所以请使用它们。例如，我在林挺使用 Jetbrains 构建，警告我关于这些问题的大部分，这也是我如何发现开发项目中的错误。

但是有很多其他的工具可以帮助你。

*   浏览器扩展
*   [W3C 标记验证服务](https://validator.w3.org)
*   由生成工具运行的林挺包

综上所述，不要依赖浏览器的容错能力，花时间检查你的代码。

帖子中没有提到的其他一些有趣的链接:

*   [浏览器如何工作](http://taligarsiel.com/Projects/howbrowserswork1.htm)，它有一个关于 [HTML 解析器的章节](http://taligarsiel.com/Projects/howbrowserswork1.htm#HTML_Parser)
*   [“原谅”被认为有害的浏览器](https://alistapart.com/article/forgiving)