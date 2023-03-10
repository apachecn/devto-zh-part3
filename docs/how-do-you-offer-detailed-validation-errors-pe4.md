# 如何支持详细的验证错误消息？

> 原文：<https://dev.to/crenshaw_dev/how-do-you-offer-detailed-validation-errors-pe4>

Sebastian McKenzie 的这条推文让我很恼火:

> 如果你正在编写验证代码，请不要让它输出“错误的 foo”或“无效栏”。请告诉我我需要修复什么，为什么无效。

我不高兴不是因为塞巴斯蒂安错了...相反，它击中了离家太近的地方。我需要为 Crutchfield 的一些输入实现更详细的验证消息，但是不清楚应该如何做。

例如，以这个电子邮件验证正则表达式:

```
^[-a-zA-Z0-9+.*!#$\/=_]+@((([a-zA-Z0-9][-a-zA-Z0-9]{0,61}[a-zA-Z0-9])|([a-zA-Z0-9]))\.)+(([a-zA-Z0-9][-a-zA-Z0-9]{0,61}[a-zA-Z0-9])|([a-zA-Z0-9]))$ 
```

Enter fullscreen mode Exit fullscreen mode

(我知道用 regex 来验证电子邮件不太好，但它对 99.999%的真实电子邮件地址都有效，而且它能帮助用户发现常见的拼写错误。)

很多事情都会导致地址验证失败，但是正则表达式只传达两种状态:*有效*或*无效*。

那么，我如何告诉用户是什么使输入无效呢？我可以想象使用上述模式的一部分来设计一系列验证:

```
 [
        {
            "pattern": "^[-a-zA-Z0-9+.*!#$\/=_]",
            "message": "Your email must start with a letter, number, or one of these symbols: - + . * ! # $ \/ = _"
        },
        {
            "pattern": "@",
            "message": "Your email address must contain @"
        }
    ] 
```

Enter fullscreen mode Exit fullscreen mode

但是有几个问题:

1.  存在代码重复，使维护复杂化。
2.  应用验证需要 JavaScript(`pattern`属性不够)。
3.  对于开发人员来说，什么是“真正的”验证(也应该在服务器端使用)以及什么只是用于有用的消息传递变得不太清楚。

这里有一个潜在的解决方案:

1.  将额外的模式称为“错别字检查”而不是“验证”，明确它们只是用来帮助解决简单的错别字，而不是提供完整的有效性检查。
2.  如果*仅*输入错误检查失败，不要将输入视为无效。如果主验证模式匹配，则将输入视为有效；如果不匹配，检查其他模式以找到“友好消息”

至少这是我目前的攻击计划。

这有意义吗？你的网站/应用程序如何处理这个问题？