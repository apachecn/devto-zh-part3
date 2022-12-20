# 什么是评论？

> 原文：<https://dev.to/codetips/what-are-comments-1e60>

> 注释只是开发者的注释

[![What are comments?](img/f1ce320139d51bec756f9cc86e7d51a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--861DcRE3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1517842645767-c639042777db%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

真的就这么简单。开发人员编写注释来帮助他们自己或其他人理解代码在将来会做什么。

每种语言都为注释指定了[语法](https://codetips.co.uk/beginner/what-is-syntax/)，就像其他任何东西一样。当你的应用程序遇到一个注释时，它会忽略它并寻找下一行可以执行的代码。所以你可以在注释中写任何你想写的东西，而不会影响你的应用程序。

例如，下面的[变量](https://codetips.co.uk/beginner/what-is-a-variable/)声明了一个正则表达式(Regex)。如果你不知道什么是 Regex 这不重要，继续读下去就好了。

```
var regex = /\b[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}\b/gi 
```

不管你懂不懂 Regex，都很难知道这是干什么的。一个注释可以让这变得更容易。

```
// Defines the regex for a valid email address
var regex = /\b[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}\b/gi 
```

现在，您马上就能确切地知道代码的作用了。

* * *

**注:**本文中的评论仅用于解释目的。你应该非常小心你的注释，因为如果你更新的是代码而不是注释，你会制造更多的混乱。

如果不编写注释，可以通过将变量的名称改为更具描述性的名称来使代码更容易理解:

```
var emailRegex = /\b[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}\b/gi 
```

在应该评论的内容上取得正确的平衡需要经验，这不是我们在本文中要讨论的内容。