# “脚本异步”属性可能不是非关键脚本的最佳选项

> 原文：<https://dev.to/simevidas/the-script-async-attribute-may-not-be-the-best-option-for-non-critical-scripts-2pld>

将`async`或`defer`属性添加到`<script>`元素会导致外部脚本被并行提取*进行解析*。这两个属性之间的区别只有在脚本完成加载后才显现出来:具有`async`属性<mark>的脚本中断解析器</mark>，而具有`defer`属性的脚本等待页面完成解析。

[![Async scripts interrupt the parser, while deferred scripts wait for the page to finish parsing](img/bced99b5fe87679ff832f9886659d038.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DxRU5y0P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webplatform.news/media/script-async-defer.png)

网页可能不想中断生成非关键内容(例如，广告)的脚本的解析器。文章“[Chrome 中 JavaScript 加载优先级](https://addyosmani.com/blog/script-priorities/)”建议对关键脚本使用`<script>`，对非关键脚本使用`<script defer>`。

> 考虑`<script async>`时要慎重。今天，它通常用于表示非关键脚本，但在低优先级加载和高优先级执行时是不一致的。

* * *

*此内容最初发布于 [https://webplatform.news](https://webplatform.news) 。*