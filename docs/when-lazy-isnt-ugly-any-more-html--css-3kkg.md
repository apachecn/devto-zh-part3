# 当懒惰不再丑陋(HTML & CSS)

> 原文：<https://dev.to/wesselbaum/when-lazy-isnt-ugly-any-more-html--css-3kkg>

[![](img/da61fc0fa3b8103a247aeb8061507bd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CZO1hizm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a01sriqp8i8jvrhs1zq4.jpg)

## 有多懒就有多丑

有些人从一开始就有足够的强迫症来编写有序的 CSS & HTML，他们永远也不会需要我想向你介绍的工具。除非他们和其他人一起工作。我绝对不是一个结构良好的代码编写者。

当我需要在 CSS 中添加一个新的类或属性时，我会在最后写出来。

当我不得不再次阅读我的代码时，我的第一个想法是“谁写了这些垃圾”，因为我经常和那些有纪律的人一起整理他们的代码。

由于这种情况经常发生，我决定寻找能够通过阅读我的代码来支持我和我的团队成员的工具。

## 漂亮的 CSS

对于我的 CSS(或 SCSS ),我使用的是 [CSSComb](http://csscomb.com/) 。使用这个工具，我可以定义我想要的 CSS 属性的顺序，如果我运行它，它将确保属性被正确排序。

## 漂亮的 HTML

对于我的 HTML 文件，我还没有找到能满足我需求的工具，所以我自己写了一个 npm 模块，它是 [class-sort](https://www.npmjs.com/package/class-sort) 。有了这个工具，我可以为类定义一个顺序，这个顺序很好地满足了我的目的。如果我还没有为一组类定义一个顺序，这些类将在定义后按字母顺序排列。

## 结论

如果你想让你的代码更干净一点(或更多)，你可以寻找适合你需要的工具。如果现在还没有，为什么不自己做呢？

这个工具的最大挑战是找到一个所有团队成员都能接受的订单结构，因为如果使用它，就会覆盖整个文件。

#### 这是我第一篇文章。请仁慈一点。