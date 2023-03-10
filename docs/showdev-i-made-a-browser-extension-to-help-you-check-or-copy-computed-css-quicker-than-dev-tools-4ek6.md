# Showdev:我做了一个浏览器扩展来帮助你比开发工具更快地检查或复制计算的 CSS

> 原文：<https://dev.to/guivr/showdev-i-made-a-browser-extension-to-help-you-check-or-copy-computed-css-quicker-than-dev-tools-4ek6>

亲爱的社区开发者:

创作者在此，乐意回答任何问题:)

# 问题

作为一名 web 开发人员，当我对 web 上我喜欢的某个元素的一些框阴影、边框半径或任何其他规则感到好奇时，我总是发现自己不得不打开开发工具。

当您使用 Dev 工具进行检查时，如果元素匹配许多 CSS 选择器，您可能需要滚动很多次才能弄清楚它的样式以及哪些规则没有被覆盖。

如果出于某种原因你想复制这些风格，那会很痛苦。或者，您可以使用 Computed Styles 选项卡——但这样您将得到所有手写形式的规则，这种形式很臃肿，它将所有单位转换为像素，忽略了%，em，calc()等，完全破坏了响应能力。

# 解

所以我构建了 [CSS 扫描](https://getcssscan.com)。有了它，无论您将鼠标悬停在哪里，您都可以立即获得该元素的干净、计算过的(但响应性好的)CSS，并且只需单击一下就可以复制它的所有规则。

[![Demo](img/e21edec7f5f83219514d2949b6d87add.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Zb1RIG9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://getcssscan.com/optimized.gif)

[CSS 扫描](https://getcssscan.com)特别适用于:

*   发现你最喜欢的网站是如何设计的；
*   调试自己的代码；
*   移动您的代码库；
*   从框架/主题/模板中复制特定的元素来使用它们，而不需要导入巨大的 CSS 文件。

这是一个兼容 Chrome、Firefox、Safari 和 Edge 的浏览器扩展。

顺便说一句，如果你有任何想法或建议来改善这个流程，我很乐意听取你的意见！

自从 9 个月前发布的第一个版本以来，我已经实现了新的特性、错误修复和一般性改进:

*   代码语法突出显示
*   显示渲染字体
*   显示元素尺寸
*   锁定框的选项
*   捷径(很多人问)
*   将 UI 按钮移到底部的选项
*   暂停扫描仪的选项
*   截断类名的选项
*   显示:分离的悬停样式
*   复制:悬停样式合并或分离
*   遍历元素并扫描父元素
*   标识元素重叠的时间
*   现在它在 iframes 上工作了！
*   更多速记规则支持
*   i18n:中文(简体和繁体)和葡萄牙语
*   重新设计用户界面和登录页面

尝试我们网站上的免费演示！T3】

到目前为止，这是一个花了我将近一年时间的作品，所以我很高兴与你分享，并真诚地想听听你对它的看法。

附注:请记住，这个扩展只是针对当你想对风格有一个整体的看法时的用例；它远远不能完全替代 Inspect Element。

第二页:第一个版本花了我整整 50 小时 17 分钟来开发。我记录了所有的发展，并提出了一个很好的速度视频！你可以在[https://www.youtube.com/watch?v=OtsNNXpXcYs](https://www.youtube.com/watch?v=OtsNNXpXcYs)查看