# 扩展 dev.to Rails 应用程序的一个最新教训是

> 原文：<https://dev.to/ben/one-recent-lesson-in-scaling-the-devto-rails-app-3154>

[dev.to](https://dev.to/) 有一个相当可扩展的架构，因为我们已经构建了它，所以大多数流量都通过我们的 CDN 快速地静态缓存在边缘[。从这个意义上来说，我们可以像静态网站一样伸缩，但是任何时候内容发生变化(比如一个新的评论，或者文章的更新，或者任何其他新鲜度的变化)，我们都要从头开始，直接回到原点。我们还为*提供一些与大多数请求异步的*动态内容。所以服务器上还是有很多负载的。](https://dev.to/ben/making-devto-insanely-fast)

像任何 Rails 应用一样，我们会遇到不同的资源限制，并在扩展时解决它们。

在解决资源瓶颈的波动时，我们基本上需要知道哪些资源正在被用尽，以及当它们被用尽时应该使用什么杠杆。因为过去非常成功的技术公司已经扩大了这个堆栈，我们很少需要那么聪明，我们只需要知道工具。

但是尽管如此，我们仍然有问题，当然。代码很难。我想分享一些我需要克服的心理障碍，以便在这方面帮助领导我们的团队。继续上课:

## 当问题出现时，回到旧材料

当我们遇到困难时，我没有重读好的材料，错误地认为问题是新的。通常这是用新的角度来看的同一个基本问题。下次我们遇到特别困难的时候，我会重读很多好材料。以开放的心态阅读大量材料，而不是纯粹为了解决问题而阅读。当你考虑这些事情的时候，不要只见树木不见森林。

在这方面，Nate Berkopec 的 Speedshop 博客和他的其他内容非常实用。我们面临的具体问题不需要在这里重新讨论，但是不要认为你需要*新*材料的建议是这里的教训。

几个 DEV 成员的东西在过去几年的平台或个人博客上也很有帮助:

[![mscccc image](img/9589039fee18bc62475670fbfa870aac.png)](/mscccc)

## 迈克·科特马什 <button name="button" type="button" data-info="{&quot;className&quot;:&quot;User&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:430,&quot;name&quot;:&quot;Mike Coutermarsh&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow user: Mike Coutermarsh" aria-pressed="false">跟随</button>

[code @github](/mscccc)![[deleted user] image](img/56fbc1337a9836e0802365a3a3022a53.png)

## [已删除的用户]

两者都绝对值得关注，因为有更多的人在 [#rails](https://dev.to/t/rails) 上发帖

最近【Mike 的这个旧帖子对思考 Heroku 资源问题特别有帮助。重读是至关重要的，因为我在记忆中混淆了一些重要的结论。

## 往前走

未来有许多挑战。例如，我们已经有一段时间没有 Postgres 作为瓶颈，但在未来它将再次成为一个瓶颈。当这种情况发生时，最好立即行动，而不是在那一刻争先恐后地学习新东西，而是彻底重读过去帮助我们克服一些小问题的旧材料。从这里，我们可以继续学习新的东西，但我们不能有这样的谬误:为了不确定的新问题而完全学习和保留旧材料。

编码快乐！