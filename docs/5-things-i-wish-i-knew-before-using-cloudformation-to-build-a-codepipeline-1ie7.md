# 在使用 CloudFormation 为 Lambda 函数构建代码管道之前，我希望知道的 5 件事

> 原文：<https://dev.to/andrewbrown/5-things-i-wish-i-knew-before-using-cloudformation-to-build-a-codepipeline-1ie7>

我的客户总是要求像联系表单、投票、选择加入表单这样的东西，我不想让我的 Rails 应用程序因为这些“微应用程序”而变得混乱。我认为，如果我可以用 CloudFormation 模板创建一个回购协议，然后我可以为每个用例分叉，这将是一个聪明的做法。如果我需要做一些小的调整，我只需点击分叉回购，变化就会自动部署。

所以我开始创建这个金色的云结构模板，它将建立:

*   DymanmoDB 表——用于存储数据
*   SNS 主题——当有人使用表单时发送电子邮件
*   API gateway——触发 lambda 函数的一种方式
*   一个 Lambda 函数——存储提交的数据并给我发电子邮件
*   S3 存储了一个 js 文件，这是一个可嵌入的脚本
*   CloudFront-serve js 文件
*   代码管道——自动构建和部署变更

一个周末项目变成了长达一周的死亡行军，进入配置地狱。我确实计划做一个关于我如何构建所说的黄金模板的教程，但是我想分享我认为可以为其他人节省大量时间来创建他们自己的黄金云模板

# AWS CodeStar 模板可供参考

AWS CodeStar 是预制的 CloudFormation 模板的一个美化的仪表板，它通过 CodePipeline 设置各种部署配置。我个人永远不会使用 AWS CodeStar，因为我不需要再增加一层复杂性。

然而。我发现有用的是模板本身。在网上很难找到好的 CloudFormation 模板来设置 CodePipeline 以部署 Lambda 函数。

我建议启动一个 CodeStar 项目，然后去 CloudFormation service 下载模板。它将节省您计算如何为您的管道组成阶段的时间。

# 通过处理过的模板理解 SAM 模板

当您设置 CodePipeline 来部署 lambda 函数时，您会添加一个部署阶段，并且您会认为您会想要添加 CodeDeploy。您实际要做的是部署一个 SAM CloudFormation 模板，它将自动设置 CodeDeploy 并部署您的 Lambda 函数。

所有 AWS 教程/资源都会告诉你使用 SAM(无服务器应用模型)模板。SAM 只是配置 CloudFormation 模板的一种不太冗长的方式。SAM 可能会因为各种原因而感到沮丧:

*   它会创造出比你想要的更多的资源
*   SAM 有几个限制，所以你最终必须使用云形成模板
*   有很多魔法在发生，这会让事情变得混乱

SAM 所做的就是将你的模板转换成完整的 CloudFormation 模板。您可以在栈的**模板**选项卡下查看输出，并选择**加工模板**。然后，我使用一个在线服务将 json 转换成 yaml，以提高可读性。阅读经过处理的模板可以更容易地调试模糊的错误，因为您对正在创建的内容有更好的了解。

# 按创建顺序删除你的栈

当你创建一个 CloudFormation 模板来为一个 Lambda 函数创建一个 CodePipeline 时，你最终会得到两个栈。第二个是λ本身。当您因为微调或调试流程而需要删除堆栈时，您最好以相反的顺序删除它们，并等待每个堆栈在另一个堆栈之前被删除。

我这样说的原因是，如果首先删除代码管道堆栈，您可能会最终删除资源(如角色),并且您会从堆栈中看到 DELETE_FAILED 错误。省省你自己的麻烦，听听我的建议

# 可怕的删除失败错误

删除堆栈时，可能会出现 DELETE_FAILED 错误。发生这种情况通常有两个原因:

1.  您的堆栈有一个想要删除的 S3 存储桶，但该存储桶不为空。
2.  您的角色不再存在，您的堆栈正在尝试删除一个不存在的资源。

在删除我的堆栈之前，我总是要清空我的 S3 桶，这很累，但这正是你必须要做的。

对于缺失的角色，您必须通过 CLI 删除堆栈，并为其提供一个存在的角色。

# 为山姆阅读 Github 问题

这应该是显而易见的，但我不认为我自己检查了 SAM 上的 Github 问题，以查看模板示例。在这里，我能够找到我通过 AWS 文档很难找到的 SAM 的语法配置。

[https://github . com/aw slabs/server less-application-model/issues](https://github.com/awslabs/serverless-application-model/issues)