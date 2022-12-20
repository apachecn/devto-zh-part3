# 交互式 JS 日期备忘单

> 原文：<https://dev.to/aprouja1/interactive-js-date-cheat-sheet-51np>

这是我的过程构建 [JSDates.dev](https://jsdates.dev) 的故事，这是我第一个发布的项目，也是我在这个过程中学到的东西。

# 为什么是日期？

对我来说，记住所有不同的 Javascript 日期和时间方法一直是一个难题。各种方法都有一个名称稍微相似的伙伴，这导致了混淆:

*   toisostring 和 toJSON
*   toUTCString & toGMTString

有些名称相似，但结果不同:

*   getFullYear
*   getUTCFullYear
*   getYear

有些名称不同，但提供的值几乎完全相同:

*   的价值
*   犹太人区

# 备忘单

考虑到所有这些，我开始创建一个工具，允许我输入多个日期，查看所有方法的所有值并进行比较。于是 [JSDates.dev](https://jsdates.dev) 诞生了！

# 工具&方法论

我是 Vue 的大力支持者已经有一段时间了，所以我知道它会是我选择的 JS 框架。CSS 框架布尔玛有一些非常有用的类，允许快速的样式。虽然我通常是一个 VS 代码用户，但我决定为这个项目试用 CodeSandbox。我以前没有在任何项目中使用过它，但是它与 Netlify 的集成非常容易使用，可以在 commit 上构建。

# 学问

*   CSS 网格真的很神奇，而且只会越来越好。建立一个反应灵敏的电网只需要几秒钟。
*   Vue 的反应系统在渲染速度方面继续给人留下深刻印象
*   日期和时间令人困惑，但是理解这些选项比添加特定的库更有价值

# 下一步

我非常努力地寻找一种方法来添加函数参数，而不用硬编码各种选项。遍历原型方法相当简单，但是试图确定这些方法带有哪些参数似乎是不可能的。

请随时告诉我您的想法或就 [Github 回购](https://github.com/aprouja1/DateCheatSheet)提出问题