# 如何修复“无法删除堆栈:角色...是无效的或不能被假定”

> 原文：<https://dev.to/david_j_eddy/how-to-fix-failed-to-delete-stack-role--is-invalid-or-cannot-be-assumed-in-aws-cloudformation-3aip>

Cross 从我的[博客](https://blog.davidjeddy.com/2019/01/10/how-to-fix-failed-to-delete-stack-role-is-invalid-or-cannot-be-assumed/)发布。

## 形势

最近几周，我大部分的工作时间都花在了[cloud formation](https://aws.amazon.com/cloudformation/)([CF](https://aws.amazon.com/cloudformation/))和 AWS 开发者工具上。 [CodeStar](https://aws.amazon.com/codestar/) ， [CodeBuild](https://aws.amazon.com/codebuild/) ， [CodePipeline](https://aws.amazon.com/codepipeline/) ，以及 [CodeDeploy](https://aws.amazon.com/codedeploy/) 。在日复一日的例行工作中，我遇到了一个让我有点困惑的错误；删除 [CF](https://aws.amazon.com/cloudformation/) 堆栈时，返回以下错误信息，且堆栈未被删除。

```
failed to delete stack: Role [...] is invalid or cannot be assumed 
```

<figure>[![](img/4209f3e64754754530e84000c3b9ed17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--azkC5O50--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/Chad_Hagan_Art_and_Design/Nautilus/Nautilus_Anthropocene.jpg) 

<figcaption>发人深省的想到了这一天:有一天我们都将成为化石。</figcaption>

</figure>

## 解决方案

错误消息文本相当清楚，尽管不是很详细。通俗地说，当 [CF](https://aws.amazon.com/cloudformation/) 服务试图承担 [IAM](https://aws.amazon.com/iam/) 角色以完成一项任务时，这个角色(在我的例子中)就不再存在了。经过一番挖掘，我找到了一个解决方案，而且这个方案非常合理！

*   转到 [IAM](https://aws.amazon.com/iam/) 服务；我们想创造一个新的角色
*   [然后](https://www.youtube.com/channel/UCPSfjD7o1CQZXzdAy56c8kg)创建一个角色名称作为错误消息中缺少的角色
*   [然后](https://www.youtube.com/channel/UCPSfjD7o1CQZXzdAy56c8kg)分配角色需要的访问策略
    *   在我的例子中，我试图删除堆栈；虽然不是最佳实践，但管理员角色已经足够了。
*   [然后](https://www.youtube.com/channel/UCPSfjD7o1CQZXzdAy56c8kg)再次执行 [CF](https://aws.amazon.com/cloudformation/) 过程

如果一切顺利，堆栈应该会顺利完成所需的操作(删除)。解决方案真的很简单。#赢了

<figure>[![](img/0cb730afb162f766e8ae1fc325630295.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rmUZaBRU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/Chad_Hagan_Art_and_Design/fortune-Magazine/Fortune_Global.jpg) 

<figcaption>所有好的图形都向上并向右。</figcaption>

</figure>

## 包扎

希望这有助于你现在或将来与 AWS 打交道。你有什么关于自动气象站[云形成](https://aws.amazon.com/cloudformation/)的快速提示吗？你对 CF 的体验如何？请在下面的评论中告诉我。