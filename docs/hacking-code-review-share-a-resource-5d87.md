# 黑客代码审查:共享资源

> 原文：<https://dev.to/nholden/hacking-code-review-share-a-resource-5d87>

*这是[黑客代码评审](https://www.hackingcodereview.com)的一部分，这是一系列你今天就可以开始使用的小技巧，让你的代码评审更有影响力。[订阅](https://www.hackingcodereview.com/#subscribe)成为第一个了解新黑客的人。*

审查代码需要一些原创的想法。当我通读提议的更改时，我会思考代码给我的感觉，以及当我遇到它时，我未来的自己可能会怎么想。我花时间收集我的想法和感受，并把它们用有帮助和有说服力的语言表达出来。

不过，通常别人已经以更好或更全面的方式说出了我的想法。开发者很幸运，互联网上充满了我们可以借鉴的想法和现有技术。在代码评审中共享资源是一个很好的方法，可以让你的评审更有影响力，而不需要太多额外的努力。

## 分享什么

共享 API 文档对于确保审阅者和作者在同一页上是非常有用的。很容易对 API 的功能做出假设，这些假设可能是真的，也可能是假的，尤其是当开发人员对语法和约定越来越熟悉的时候。链接到文档可以确保评审者和作者对他们使用的 API 有一个共同的理解。

> 这个是我们在这里使用的`distinct`吗？既然我们想要不同的记录，我认为我们不需要争论。

博客文章是代码评审中另一个有用的资源。当我遇到特定类型的问题时，我总是会回头参考一些博客帖子，比如[时区](https://thoughtbot.com/blog/its-about-time-zones)、[字符编码](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)，或者[表单 UI](https://blog.prototypr.io/7-rules-of-using-radio-buttons-vs-drop-down-menus-fddf50d312d1) 。开始建立一个有帮助的博客文章库，当它们相关时，把它们放到代码评审中，这样你就可以和作者发展一些共同的知识。

> 我在[这篇很棒的博客文章](https://www.speedshop.co/2019/01/10/three-activerecord-mistakes.html)中了解到`exists?`总是执行 SQL 查询。我们应该记住这一点还是考虑使用`present?`来避免多次查询？

团队做出各种各样没有正式记录的决策。为了揭示代码审查中的一些问题，我分享了旧问题、合并的拉请求和现有代码的链接。通过讨论、以前的代码审查和提交消息，我可以帮助拼凑出为什么我们以一种特定的方式解决了一个问题，以及再次采用这种方法是否有意义。

> 我真的很喜欢发布的用户体验(#105160)，它禁用了表单，并为标记为垃圾邮件的用户提供了一个解释性说明。你认为在这里采取类似的方法怎么样？

## 如何分享

共享资源时提供上下文。在评论中删除一个链接本身是很神秘的，它会让作者猜测你在建议他们做什么。如果你清楚地表明你的意图是分享一些可能有帮助的东西，而不是因为不知道一些你已经知道的东西而羞辱他们，那么作者更有可能欣赏这些资源。

> VCR 有一些[请求匹配](https://relishapp.com/vcr/vcr/v/1-6-0/docs/cassettes/request-matching)选项，这可能意味着我们不需要准确获得这些 URL 来重用想要的磁带。我想知道这是否值得探讨，这样我们就可以将磁带与测试分离一点。

将一种资源与一种赞美结合起来会特别有效。如果你欣赏作者的方法之一，并且你已经看到它在其他地方执行得很好，分享它。这是给作者一些额外肯定的好方法，也让他们下次遇到类似问题时有所思考。

> 我喜欢这个新的`.text-center`类的可重用性。听说过[顺风 CSS](https://tailwindcss.com) 吗？这是一个像这样的实用类的整体框架。如果我们喜欢这种风格，也许我们可以考虑将来使用它。

在代码审查中，一个链接胜过千言万语。为自己节省一些击键次数，并寻找机会分享有用的资源。

迈克尔·D·贝克威思在 [Flickr](https://flic.kr/p/r3zodL) 上的封面图片。