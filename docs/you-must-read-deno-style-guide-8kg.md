# 你必须阅读:德诺风格指南。

> 原文：<https://dev.to/qm3ster/you-must-read-deno-style-guide-8kg>

下午好。
事不宜迟，请去阅读 [Deno 风格指南](https://deno.land/manual/contributing/style_guide)
尤其是如果你不使用 [deno](https://deno.land/) ，并且近期内也不打算尝试的话。

如果没有明显的原因，你想阅读一些中间有表情符号的摘录，这里有一些这个小文件中的许多精华:

> 1.  作为公共 API 一部分的函数需要 0-2 个必需的参数，外加(如果需要的话)一个选项对象(所以总共最多 3 个)。
> 2.  可选参数通常应该放在 options 对象中。
>     
>     
> 3.  “options”参数是唯一的常规“Object”参数。
>     
>     
>     
>     其他参数可以是对象，但它们必须在运行时与“普通”对象相区别，方法是:
>     
>     
>     *   有区别的原型(例如`Array`、`Map`、`Date`、`class MyThing`)
>     *   众所周知的符号属性(例如带有`Symbol.iterator`的 iterable)。
> 
> 这允许 API 以向后兼容的方式发展，即使当选项对象的位置改变时。

[![Finger Up](img/1b1a3de669469f8fca92fd8a5b8a6c5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mRnXSldx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://emojipedia-us.s3.dualstack.us-west-1.amazonaws.com/thumbs/120/apple/155/white-up-pointing-index_261d.png)

> ### 不鼓励元编程。包括使用代理人。
> 
> 即使这意味着更多的代码，也要明确。

[![Palms Up Together](img/e2efbb4400af60e2f8b3941131f6cc36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eirtpK2E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://emojipedia-us.s3.dualstack.us-west-1.amazonaws.com/thumbs/120/google/146/person-with-folded-hands_1f64f.png)

> 除非它们的意图不明显，否则不要记录函数参数(尽管如果它们不明显，无论如何都应该重新考虑 API)。因此一般不应该使用 [@param](https://dev.to/param) 。

[![Fire](img/caa919b6568e95e2c782e6cb640644c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zrynz5iK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://emojipedia-us.s3.dualstack.us-west-1.amazonaws.com/thumbs/120/facebook/65/fire_1f525.png)

> 代码示例不应包含附加注释。它已经在注释中了。如果它需要进一步的评论，这不是一个好例子。

[![Sick Burn](img/00eddae6200c2a0914e29d9285525d5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dzkCsN2U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qph.fs.quoracdn.net/main-qimg-7033abdf25ceaa80cd7f7a2df789336c.webp)

如果你想为你的项目窃取它，[这就是 markdown 住的地方](https://github.com/denoland/deno/blob/master/website/style_guide.md)。

[![Deno Logo Animated](img/5d34e11c9b9e498ce7d18dde97bf3f7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BqY7wmkg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://deno.land/deno_logo_2.gif)

<small><small>**免责声明**:作者与 deno 项目或其作者无关。所有图像的版权属于其各自的所有者。</small></small>

玩得开心！