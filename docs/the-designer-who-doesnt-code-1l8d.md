# 不编码的设计师

> 原文：<https://dev.to/metamn/the-designer-who-doesnt-code-1l8d>

关于设计师是否应该编写代码的争论毫无意义却很流行。搜索查询返回了 27，800 个结果。

我一直在等待一个不言自明的案例研究。让我们看看。

## Comps

是的，2019 年我们仍然会收到设计师的样品。无论是来自 Photoshop 还是 Sketch，是否打包到 Zeplin 中，对于现代前端开发人员来说，它们都是不可用的。

像 90 年代一样，人们必须对像素进行切片、计算、转换成弹性度量。必须弄清楚响应式网格结构、响应式排版、所有可重用的设计元素——对它们进行编码——并开始添加每个 comp 附带的所有例外，破坏源代码和项目。

如果没有设计思维，就没有设计系统和代码、项目、预算，人就会变得臃肿。

## 任务

听起来很简单:在主页上以一种回应的方式列出最新的帖子。在大屏幕上应该有 8 个帖子，每排 4 个，在中等屏幕上 6 个帖子/ 3 个一排，在平板电脑上 4 个帖子/ 2 个一排，在移动设备上每个帖子 2 个一排。

[![The comp on large screens](img/4ca6fdf6f61e19949033b7adc84ad1c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RSli6KnO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4eg94ji9521msbfp8i7e.png)

[![The comp on medium screens](img/afddb68e550c1ea9b2de7b545dddbe15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x20-NGHH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tem1pzhd7mtxfy78c7cn.png)

[![The comp on tablet](img/47a5b905e97561ad0322400c7e8f5c7a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--53qBpHM6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fqhujxlhkngi3c7jrtxd.png)

[![The comp on mobile](img/78a6166fd93d63a481797f2e886aa8fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UMw73UsD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v7slu9jhyxjf3qyi8avq.png)

为了保护设计师和公司，构图被模糊了。黑色和红色的框是广告占位符，在模糊的截图无法显示的栏之间有灰色的垂直线。

使用实体模型复制复合材料。你可以在 Codepen 上实时查看[以更好地感知任务。](https://codepen.io/metamn/pen/OdjaGE?editors=0100#0)

[![The comp on large screens replicated with mockups](img/0b551ad76239f5be10d5b187d27733e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k4nXPsEJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9vxt62y6ybuwoez632ai.png)

## 问题

因为设计者不编码...

> 设计者不知道内容不能以响应的方式获得，只能以响应的方式显示。

...并创建 comps，使开发者在后端获取所有 8 个帖子，并在前端隐藏其中一些帖子，这是一种性能浪费，需要那些资源最少的设备来支持。

> 设计者不知道 CSS 网格规范

...并用竖线分隔各列来增加设计的趣味。CSS 网格不支持[网格间隙样式](https://stackoverflow.com/questions/45884630/css-grid-is-it-possible-to-apply-color-to-grid-gaps)和[肮脏的黑客](https://stackoverflow.com/questions/13792755/show-border-grid-lines-only-between-elements/47914693#47914693)必须用来完成这个原始的设计思想。

> 设计者不知道异常是痛苦的，并且会膨胀源代码

...并在两行之间插入广告。广告有不同的尺寸和定位，打破了网格。必须添加一个例外来支持这一设计决策，在开发期间和以后的维护期间会使代码膨胀。

## 方案

如果设计者正在编码，则采取以下设计决策:

1.  不要在较小的屏幕上隐藏文章——将它们包装到一个导航元素中。这样，用于在后端获取所有帖子的资源就不会浪费在前端。

2.  忘记垂直列分隔线。它们无法用当前的最佳实践来实施，需要变通方法。让形式跟随功能，即使功能是编码标准。

3.  把广告放在别处。结合 2。)很危险。离开网格将节省大约一半的开发时间和预算。

有了这些实际的设计决策，实现就会很容易:使用 CSS 网格。花费的时间从几分钟到半小时左右。

对于垂直边框，CSS 网格技术——到目前为止在整个网站上使用——不能重复使用。必须进行研究，以了解规范最近是否添加了网格间隙样式功能，或者是否有聚合填充或好的解决方法。

事实证明，只存在一个解决方法，它不能完全解决问题，因为建议的边界是...复杂:它适用于中间的列，将边缘的列视为例外。

研究和解决方法花费的时间:大约一个小时。

要实现 comp，必须在网格元素上创建一个循环，只在必要的地方添加边框。为此，我已经在 Flexbox 上构建了一个组件。导入、调整到当前项目需要大约半个小时。如果要从头开始写，至少要花一个多小时。

在帖子之间插入广告要复杂得多。它是关于在现有的运行良好的代码中添加异常。必须修改显示帖子列表的后端代码才能插入广告；在前端，网格必须重新绘制和重新界定。

最终的前端代码是如此丑陋以至于[值得一看](https://codepen.io/metamn/pen/OdjaGE?editors=0100#0)。

花费时间:大约一小时。

## 数字

The cost of the solutions

| 解决办法 | 持续时间(分钟) | 笔记 |
| --- | --- | --- |
| 实际实施 | Thirty | 使用 CSS 网格 |
| 垂直边框—研究 | Sixty | 浪费了。只找到一个讨厌的黑客 |
| 垂直边框—实施 | Thirty | +60 分钟，如果尚未编写 Flexbox 网格组件 |
| 行间广告 | Sixty | 结果是一个非常丑陋的补丁覆盖了一个写得很好的代码 |

The amount and cost of new code added

| 新代码 | 代码行(LOC/SLOC) | 花费的时间(分钟) |
| --- | --- | --- |
| [post-list-with-ad.php](https://gist.github.com/metamn/b052d61cc36ae86a4c0de4e7fed38638) | 37 / 32 | Fifteen |
| [flexbox-grid 混合](https://codepen.io/metamn/pen/OdjaGE) | 62 / 54 | Sixty |
| [响应式 flexbox 网格混合](https://codepen.io/metamn/pen/OdjaGE) | 48 / 39 | Fifteen |
| [flexbox-grid-borders 混合](https://codepen.io/metamn/pen/OdjaGE) | 30 / 26 | Fifteen |
| [最新帖子 mixin 新代码](https://codepen.io/metamn/pen/OdjaGE) | 15 / 15 | Fifteen |
| [latest-posts-regrid-for-ad mixin](https://codepen.io/metamn/pen/OdjaGE) | 108 / 97 | Forty-five |
| 总数 | 300 / 263 | One hundred and sixty-five |

Code bloat

| 成分 | 操作 | 例外 |
| --- | --- | --- |
| 带广告的帖子列表 | 增加 | 它可能不会再次被重复使用 |
| Flexbox 网格 | 增加 | 它可能不会再次被重复使用 |
| 响应式 flexbox 网格 | 增加 | 它可能不会再次被重复使用 |
| 响应网格 | 跳过 | 现在不再重复使用，而是在整个站点重复使用 |

## 痛苦

这个项目有几十个类似上面描述的*最新帖子*的部分。一半以上都是用同样的*无法实现的*方式设计的。或者，用同样*昂贵的可实施的*方式。

请记住:仅在这一部分，我们就花了至少 2.5-3 个小时，而不是半个小时。是的，

> 比平时多 5 倍或 500%。

这是巨大的。如果你把它们加起来，考虑到长期的技术债务，你会立刻知道*“设计师应该编码吗？”*。