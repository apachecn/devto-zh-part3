# 修复自动缩放组未附加到目标组的 AWS 代码部署问题

> 原文：<https://dev.to/cvortmann/fixing-aws-codedeploy-issue-where-auto-scaling-group-is-not-attached-to-target-group-47ac>

AWS CodeDeploy 蓝/绿部署可以自动复制以前的*自动缩放组*配置。我们艰难地发现的一件事是:在一次部署后，新创建的*自动扩展组*，它有一个*目标组*，不再有它。

[![Auto Scaling Group missing Target Group](img/906cc3199c88ae5fcd13d8caa172df66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gAJYczLR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nvparwq0vqly9nbil3ct.png)

这是一个尚未修复的已知问题。你可能已经注意到了，或者你可能已经注意到了，就像在我们的例子中，由于一次大停电。

## 这怎么会导致停电呢？

*目标组*负责检查实例的健康状况，当实例不健康时，将它们从*负载均衡器*中移除。被配置为保持固定数量的实例的*自动缩放组*负责启动新的实例来替换不健康的实例，并在替换报告为健康后终止不健康的实例。

由于 CodeDeploy 创建的*自动伸缩组*没有连接到 CodeDeploy 从其复制信息的原*自动伸缩组*使用的现有*目标组*，不健康的实例将被*目标组*从*负载平衡器*中移除，但是新的*自动伸缩组*不会启动新的实例。

如果您的不健康实例不能自行恢复，这意味着您的应用程序将完全关闭。在我们的情况下，我们使用了一个版本的 Erlang VM，它会突然崩溃并使我们的实例不健康。

## 我们如何修复它？

我们设想，作为部署过程的一部分，我们可以将*目标组*附加到新创建的*自动扩展组*中。正如在[文档](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html#reference-appspec-file-structure-hooks-run-order)中看到的，蓝/绿部署的最后一个挂钩是`AfterAllowTraffic`挂钩，所以我们决定将它们附加在其中。

我们已经创建了一个简单的 bash 脚本，它使用 AWS 命令行界面(CLI)来查找必要的信息，然后再次使用 CLI 将*目标组*附加到*自动缩放组*。你可以在[官方文档](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html#reference-appspec-file-structure-environment-variable-availability)中看到钩子可以获取信息的环境变量。

```
targetGroupName=$(aws deploy get-deployment --deployment-id $DEPLOYMENT_ID --query "deploymentInfo.loadBalancerInfo.targetGroupInfoList[*].name" --output text)
targetGroupArn=$(aws elbv2 describe-target-groups --names $targetGroupName --query "TargetGroups[*].TargetGroupArn" --output text)
autoscalingGroupName=$(aws deploy get-deployment --deployment-id $DEPLOYMENT_ID --query "deploymentInfo.targetInstances.autoScalingGroups" --output text)
aws autoscaling attach-load-balancer-target-groups --auto-scaling-group-name "$autoscalingGroupName" --target-group-arns "$targetGroupArn" 
```

为了将*目标组*附加到*自动缩放组*，我们需要*自动缩放组的*名称和*目标组的*亚马逊资源名称(ARN)。我们使用当前部署的`$DEPLOYMENT_ID`找到*目标组*的名称，然后使用它找到*目标组*的 ARN。我们使用与我们用来查找*目标组*名称相同的调用来查找*自动缩放组*的名称，但是使用不同的查询。最后，我们使用`attach-load-balancer-target-groups`命令连接它们。

值得一提的是，为了能够运行这些命令，您的实例需要以下操作的权限:

*   `codedeploy:GetDeployment`
*   `elasticloadbalancing:DescribeTargetGroups`
*   `autoscaling:AttachLoadBalancerTargetGroups`

这修复了在部署后*自动缩放组*没有连接到*目标组*的问题。但是当*自动缩放组*决定启动一个新实例时会发生什么呢？

## 自动缩放组决定启动一个新实例

当*自动扩展组*决定启动一个新实例时，该实例将在启动后触发一次新的部署。但是部署者具有不同**启动事件**。一个正常的蓝/绿部署将有一个**启动事件**作为`user`，而由*自动缩放组*动作创建的部署将有一个**启动事件**作为`autoScaling`。

不过，对我们来说重要的是，我们在最后一节中的简单脚本所使用的信息会有所不同。更具体地说，由于这次部署没有复制*自动缩放组*的配置，所以没有关于*自动缩放组*的信息。所以我们查找`autoscalingGroupName`的命令将返回`"None"`。这会导致实例部署失败的无限循环，并且*自动扩展组*会启动新的实例并尝试再次部署到它们。

由于在蓝/绿部署期间，当脚本在`AfterAllowTraffic`中运行时，*目标组*已经附加到了*自动缩放组*，我们可以更改我们的脚本，仅在`autoscalingGroupName`不同于`"None"`时附加它们。

```
targetGroupName=$(aws deploy get-deployment --deployment-id $DEPLOYMENT_ID --query "deploymentInfo.loadBalancerInfo.targetGroupInfoList[*].name" --output text)
targetGroupArn=$(aws elbv2 describe-target-groups --names $targetGroupName --query "TargetGroups[*].TargetGroupArn" --output text)
autoscalingGroupName=$(aws deploy get-deployment --deployment-id $DEPLOYMENT_ID --query "deploymentInfo.targetInstances.autoScalingGroups" --output text)

if [ "$autoscalingGroupName" != "None" ]
then aws autoscaling attach-load-balancer-target-groups --auto-scaling-group-name "$autoscalingGroupName" --target-group-arns "$targetGroupArn"
fi 
```

## 结论

在我们的部署过程中运行这个脚本有助于我们减少停机。请记住，在我们等待 AWS CodeDeploy 方面的问题得到解决时，我们将前面提到的 bash 脚本的使用视为一种临时修复。

在对导致停机的情况进行进一步测试和模拟之后，我们现在确信，对不健康实例的替换正在按预期进行。这给我们带来了极大的内心平静。