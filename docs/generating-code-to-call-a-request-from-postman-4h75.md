# 生成代码以调用来自邮递员的请求

> 原文：<https://dev.to/praneetnadkar/generating-code-to-call-a-request-from-postman-4h75>

Postman 是我用来调用、测试和调试 API 的最好的工具之一。今天早上，当我在处理一些事情的时候，我发现了《邮差》中两个我没有意识到的出色特性。Postman 可以为您生成一些请求调用代码，也可以生成文档。对于那些不知道的人来说，是的，这是真的！

我已经用 C#创建了一个示例 API，看起来是这样的:

```
// POST api/values
[HttpPost]
public ActionResult<string> Post([FromBody] string value)
{
   return ""+value+" is posted";
} 
```

Enter fullscreen mode Exit fullscreen mode

现在在 Postman 中调用这个 API，它看起来会像这样:

[![Post API](img/25960fea315ef912a4fff46f41c5c646.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g-mzKi1k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wdbgp8776odxf8cmxxcf.PNG)

现在，为了生成一些代码来调用这个请求，在右上角有一个 code 按钮。点击它来查看选项，在那里你可以看到许多语言来生成代码。它看起来像这样:

[![Code button in postman](img/d50a2997ebf0d359b09b7240dace33dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vkPHK5gp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7uho0wceo18wit7sxwbb.PNG)

当您单击此代码按钮时，会出现一个类似这样的弹出菜单:

[![Code clicked](img/7a3e6ab24269dded0dfbe4951c241494.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--caqLCQ0p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xuxcmoo9yzj662y7apvy.PNG)

现在，您可以看到有一个代码片段和一个下拉框，您可以从中选择许多选项，以确定要为哪个选项生成代码。可用的选项有:

[![code_options](img/718ad4cdb8aa84ffd61b4cccad328124.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jm4K8E4T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vxgc74yaygat8xfhpxn1.PNG)

现在，单击任何选项，您将看到代码。复制代码并粘贴到您的 IDE 中，然后运行！

大概是这样的:
[![code_snippet](img/c72ddeb4d377395bdceb7553b8fd74c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WiiDWfHI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4l68b2j27kbz6dsysrhi.PNG)

搞定了！就这么简单。