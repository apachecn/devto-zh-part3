# 在 CC 中点击按钮改变 PC 的 CSS？

> 原文：<https://dev.to/nishugoel/change-css-of-pc-on-button-click-in-cc-3m91>

从这篇博文开始，让我把重点放在我在标题中使用的一些术语上。因此，议程是借助子组件中的按钮来更改父组件的 CSS。

对于那些完全不熟悉 Angular 的人来说，CSS 代表级联样式表，可以帮助开发人员设计他们的 Angular 应用程序。随着 Angular 7 的发布，当我们使用 CLI 创建新的 Angular 应用程序时，它会询问我们希望将哪种样式表格式添加到我们的应用程序中。请参见下图:

[https://thepractical dev . S3 . Amazon AWS . com/I/z 8 x hea 1 ldb 5 xlz 6 o 4 gab . png](https://thepracticaldev.s3.amazonaws.com/i/z8xhea1ldb5xlz6o4gab.png)

在选择了方便的样式表格式 CSS 之后，我们将在应用程序中创建两个组件。

要使用 CLI 创建组件，我们使用:

ng g c 组件名称

首先，在组件的模板中创建一个按钮。

[https://gist . github . com/NishuGoel/c 63 e 6e 603 c 94 fb1c 466 BC 1288 DD 121 e 9](https://gist.github.com/NishuGoel/c63e6e603c94fb1c466bc1288dd121e9)

现在，要访问这个按钮来更改父组件上的 CSS，我们使用@Output()和 EventEmitter。为了做到这一点，

[https://gist . github . com/NishuGoel/E6 BCF 39205 DD 4c 7 bbfea 2 ea 272 ef 73 b 5](https://gist.github.com/NishuGoel/e6bcf39205dd4c7bbfea2ea272ef73b5)

现在，让我们转到其他组件来使用此按钮并更改 CSS。

[https://gist . github . com/nishugoel/3 CEA 7023 c 8933 b 001 e 7d 4 ca 7 B1 d 30690](https://gist.github.com/NishuGoel/3cea7023c8933b001e7d4ca7b1d30690)

父组件看起来像这样。

[https://gist . github . com/NishuGoel/67 df 7 AAC 7 e 60 c 09 a 83 d6e 5c 9017 AC 6a 1](https://gist.github.com/NishuGoel/67df7aac7e60c09a83d6e5c9017ac6a1)

如果我们现在看一下 CSS 文件，它对于理解本文来说非常简单。

[https://thepractical dev . S3 . Amazon AWS . com/I/e 2 bl R1 wbtmlmc 9 FP 3 uvi . png](https://thepracticaldev.s3.amazonaws.com/i/e2blr1wbtmlmc9fp3uvi.png)

做完这些，现在让我们看看结果:

[https://thepractical dev . S3 . Amazon AWS . com/I/z 6 nw9 hoslmmu 3 gie qne 2 . gif](https://thepracticaldev.s3.amazonaws.com/i/z6nw9hoslmmu3gieqne2.gif)