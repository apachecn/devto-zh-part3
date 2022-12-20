# 对基于第三方物流引擎的 openapi 工具船的反馈

> 原文：<https://dev.to/johndcarmichael/feedback-on-a-tpl-engine-based-openapi-tool-boats-4140>

大家好，

我正在寻找任何关于我刚刚发布的新工具的反馈/评论。

[https://www.npmjs.com/package/boats](https://www.npmjs.com/package/boats)

它使人们能够使用模板引擎 Nunjucks 编写更加动态的 openapi 规范文件。

我发现编写 openapi 文件非常重复，因为无法将内容注入到可重用的块中，例如，编写 json api 风格的输出意味着一遍又一遍地编写相同的顶级属性，或者新的 openapi 3 声明 json 响应。作为一个试验性的解决方案，我在包 2 中写入了辅助函数“mixin”和“uniqueOdId”。

Mixin 函数的行为很像 sass/less mixin，允许您将代码注入到可重用的块中。

UniqueOdId 返回一个基于文件系统中文件位置的字符串，这对于和代码生成器非常重要；)

不管怎样，我想我会把它扔进开源世界，看看它是下沉还是游动还是漂浮。

干杯，约翰