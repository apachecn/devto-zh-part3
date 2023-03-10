# 使用 JQ 和管道将 Cognito 电子邮件写入文件的快速技巧

> 原文：<https://dev.to/dvddpl/quick-tip-to-write-cognito-emails-to-file-using-jq-and-pipe-o1j>

上周，我需要以某种方式检索我们公司内部使用的一个应用程序的 Cognitor 用户池中用户的所有电子邮件。
[![Cognito User Pool](img/eb6932df25afc57c99f1bce12090f6ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7NL50vgA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vo5wisxtet4mtrbcluge.png) 
他们不是很多，我可能会花 30 分钟浏览 AWS UI 控制台中列出的所有用户，然后将他们复制粘贴到 excel 文件中。
但是我讨厌手动做事，尤其是那些枯燥和重复的任务，因此，我迅速打开了 [AWS Cognito 文档](https://docs.aws.amazon.com/cli/latest/reference/cognito-idp/index.html)和 [JQ 教程](https://stedolan.github.io/jq/tutorial/)来弄清楚如何快速获取邮件列表:
这是一小段漂亮的代码，它使我免于成为一只复制的猴子:

```
aws cognito-idp list-users --user-pool-id YOUR_USER_POOL | jq '[.Users[].Attributes[].Value | match(".*@.*") | .string]' > emails.json 
```

Enter fullscreen mode Exit fullscreen mode

是的，**也许我仍然花了大约 20 分钟才弄明白，但通过这样做，我有机会学习了一些关于 AWS Cognito API 的新知识，刷新了我的 JQ 解析技能，我有一个小片段，我团队中的任何人都可以在类似任务出现时使用。**

[![Tadaa!](img/46106ed8357f9c9555c3dd5da355e429.png)](https://i.giphy.com/media/xUA7aPEybD1cLJl6cU/giphy.gif)

* * *

它在做什么？

`aws cognito-idp list-users --user-pool-id YOUR_USER_POOL`只是请求 AWS API 从特定的 Cognito 用户池中检索用户
`|`管道将 API 调用的结果作为 JQ 命令
`.Users[].Attributes[].Value`的输入参数获取嵌套在 API 响应中的每个用户的所有属性值
`|`将结果提供给下一个正则表达式
`match(".*@.*")`，后者搜索电子邮件值
`|`将正则表达式找到的电子邮件列表提供给最后一个 JQ 命令
`.string`，后者从正则表达式中提取字符串值
邮件列表被很好地打包成一个数组(见整个 JQ 命令
`> emails.json`开头和结尾的括号)将管道命令的结果写入一个文件。(`>`总是创建一个新文件，而`>>`将追加到内容中，如果存在的话)

其他提示和工具[此处](https://dev.to/dvddpl/tools-i-wish-i-knew-from-the-start-48kl)