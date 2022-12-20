# Go 包装(结构)设计

> 原文：<https://dev.to/ballweera/go-package-structure-design-b0m>

[![Package](img/ae3be56c91ff74798dc2c1ce38ca163c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SiNbBAvs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1512418490979-92798cec1380%3Fixlib%3Drb-1.2.1%26auto%3Dformat%26fit%3Dcrop%26w%3D1500%26q%3D80) 

<figcaption>照片由莱昂·温特在 Unsplash</figcaption>

上拍摄

> 好的包装名称告诉你它提供什么，而不是它包含什么

我相信以用户为中心的设计。这不仅仅是关于 UX/UI。它是关于事物将如何被使用。所以当我创建函数、程序、API 的时候，我总是会考虑如何使用它们。这也是我喜欢测试驱动开发(TDD)方法的原因，因为它鼓励我思考如何使用我的功能(测试)。

* * *

包是 Go 程序中其他包或模块使用它的单元。当我创建一个包时，我会询问这个包将如何使用。

## 在 Go 中，我们总是通过包来使用函数。

包名在 Go 中是必不可少的。这是指导方针，

> 包名在您的 Go 程序中必须是唯一的

如果您发现包名称在您的 Go 程序中被多次使用，那么这些包可能太普通了，或者存在一些需要验证的设计问题。

为了演示如何创建 Go 包，请考虑电子商务上的购物车。我将提供“添加到购物车”功能。我从购物车包开始。

> 当你创建一个 Go 包时，总是把 Go 文件的名字作为包的名字。

每次我创建一个新的包，我总是把 Go 文件和包名放在一起。我将在这个 Go 文件中维护这个包的所有功能，直到我理解了这个包的边界，然后使用另一个 Go 文件来重新组织这个包。如果一些特性在这个包中没有意义，我会把它移到一个新的包中。