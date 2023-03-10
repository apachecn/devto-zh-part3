# 从 4 年的云形成工作中获得的经验教训

> 原文：<https://dev.to/moleculeman/lessons-learned-from-4-years-of-working-with-cloudformation-n72>

4 年前，我开始研究 cloudformation。从那以后，我搞砸了许多基础设施。其中一些已经投入生产。但是每次我把事情搞砸了，我也学到了新的东西。由于我的无知，4 年后我可以分享我学到的一些最重要的教训。

## 第 1 课:在部署变更集之前验证变更

我在云形成的旅程中很早就学到了这一课。我不记得我到底破坏了什么，但我记得当时我是使用`aws cloudformation update`命令部署我的堆栈的。这个命令只是推出您的模板，而没有对要部署的更改进行任何类型的验证。我认为不需要解释您需要验证您理解您将要部署的内容。

在那次搞砸之后，我立即改变了我的部署管道，用`create-change-set`命令
替换了`update`命令

```
# OPERATION is either "UPDATE" or "CREATE"
changeset_id=$(aws cloudformation create-change-set \
    --change-set-name "$CHANGE_SET_NAME" \
    --stack-name "$STACK_NAME" \
    --template-body "$TPL_PATH" \
    --change-set-type "$OPERATION" \
    --parameters "$PARAMETERS" \
    --output text \
    --query Id)

aws cloudformation wait \
    change-set-create-complete --change-set-name "$changeset_id" 
```

创建变更集时，它不会以任何方式影响现有的堆栈。与`update`命令不同，变更集方法不会导致实际的部署。相反，它创建了一个变更列表，您可以在实际部署之前查看它。您可以在 aws 控制台 gui 中查看更改。但是如果你有自动化一切的心态，那么你可能更喜欢在你的 cli 中检查它们:

```
# this command is presented only for demonstrational purposes.
# the real command should take pagination into account
aws cloudformation describe-change-set \
    --change-set-name "$changeset_id" \
    --query 'Changes[*].ResourceChange.{Action:Action,Resource:ResourceType,ResourceId:LogicalResourceId,ReplacementNeeded:Replacement}' \
    --output table 
```

该命令应该产生类似于下面的输出:

```
--------------------------------------------------------------------
|                         DescribeChangeSet                        |
+---------+--------------------+----------------------+------------+
| Action  | ReplacementNeeded  |      Resource        | ResourceId |
+---------+--------------------+----------------------+------------+
|  Modify | True               |  AWS::ECS::Cluster   |  MyCluster |
|  Replace| True               |  AWS::RDS::DBInstance|  MyDB      |
|  Add    | None               |  AWS::SNS::Topic     |  MyTopic   |
+---------+--------------------+----------------------+------------+ 
```

特别注意`Action`为`Replace`、`Delete`或`ReplacementNeeded`为`True`的变化。这些是最危险的更改，通常会导致一些数据丢失。

当审查变更时，可以用
部署它们

```
aws cloudformation execute-change-set --change-set-name "$changeset_id"

operation_lowercase=$(echo "$OPERATION" | tr '[:upper:]' '[:lower:]')
aws cloudformation wait "stack-${operation_lowercase}-complete" \
    --stack-name "$STACK_NAME" 
```

## 第 2 课:使用堆栈策略防止替换或删除全状态资源

嗯，有时候仅仅回顾变化是不够的。我们都是人，都会犯错误。在我们开始使用变更集后不久，我的一个队友不知不觉地执行了部署，导致数据库被重新创建。因为是测试环境，所以没有造成伤害。尽管我们的脚本显示了变更列表并要求确认，但是`Replace`变更被忽略了，因为变更列表太大了，不适合屏幕。由于这是测试环境中的例行更新，所以没有太多的注意力放在这些变化上。

有些资源是你永远不想被替换或删除的。这些是全状态服务，如 RDS 数据库实例或 elastichsearch 集群等。如果所执行的操作需要删除这样的资源，aws 会自动拒绝部署，那就太好了。幸运的是，cloudformation 有一个内置的方法来做到这一点。这就是所谓的堆栈政策，你可以在[文档](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/protect-stack-resources.html) :
中了解更多

```
STACK_NAME=$1
RESOURCE_ID=$2

POLICY_JSON=$(cat <<EOF {
    "Statement" : [{
        "Effect" : "Deny",
        "Action" : [
            "Update:Replace",
            "Update:Delete"
        ],
        "Principal": "*",
        "Resource" : "LogicalResourceId/$RESOURCE_ID"
    }]
} EOF )

aws cloudformation set-stack-policy --stack-name "$STACK_NAME" \
    --stack-policy-body "$POLICY_JSON" 
```

## 第三课:使用秘密参数更新堆栈时使用 UsePreviousValue

当你创建 RDS mysql 实例时，aws 要求你提供`MasterUsername`和`MasterUserPassword`。因为不能在源代码中存储秘密，而且我想完全自动化所有的事情，所以我实现了一个“巧妙的机制”,在部署之前，将从 s3 中检索凭证，如果找不到凭证，将生成新的凭证并存储在 s3 中。然后，这些凭证将作为参数传递给 cloud formation create-change-set 命令。在使用脚本进行试验时，一旦与 s3 的连接丢失，就会发生这种情况，我的“巧妙机制”会将其视为生成新凭证的信号。如果我开始在生产中使用这个脚本，并且这个连接问题再次出现，它将使用新的凭证更新堆栈。在这种特殊情况下，可能不会发生什么坏事。然而，我还是放弃了我的“聪明的机制”,开始使用不太聪明的方法，只提供一次凭证——当栈被创建时。稍后当堆栈需要更新时，我会使用`UsePreviousValue=true` :
而不是指定秘密参数值

```
aws cloudformation create-change-set \
    --change-set-name "$CHANGE_SET_NAME" \
    --stack-name "$STACK_NAME" \
    --template-body "$TPL_PATH" \
    --change-set-type "UPDATE" \
    --parameters "ParameterKey=MasterUserPassword,UsePreviousValue=true" 
```

## 第四课:使用回滚配置

我工作的地方的另一个团队开始使用称为回滚配置的云形成特性。我以前从未见过它，并很快意识到它将使我的云编队栈的部署更加防弹。现在，每当我通过 cloudformation 将代码部署到 lambda 或 ECS 时，我都会使用它。它是这样工作的:当您创建变更集时，在`--rollback-configuration`参数中指定 cloudwatch alarm arn。稍后，当您执行变更集时，aws 会监视此警报至少一分钟。如果在此期间报警状态变为`ALARM`，则它会回滚部署。

下面是 cloudformation 模板的一个示例摘录，其中我创建了一个 cloudwatch 警报，该警报监视 cloudwatch 日志中错误数量的自定义 cloudwatch 指标(指标是通过 MetricFilter 创建的):

```
Resources:
  # this metric tracks number of errors in the cloudwatch logs. In this
  # particular case it's assumed logs are in json format and the error logs are
  # identified by level "error". See FilterPattern
  ErrorMetricFilter:
    Type: AWS::Logs::MetricFilter
    Properties:
      LogGroupName: !Ref LogGroup
      FilterPattern: !Sub '{$.level  =  "error"}'
      MetricTransformations:
      - MetricNamespace: !Sub "${AWS::StackName}-log-errors"
        MetricName: Errors
        MetricValue: 1
        DefaultValue: 0

  ErrorAlarm:
    Type: AWS::CloudWatch::Alarm
    Properties:
      AlarmName: !Sub "${AWS::StackName}-errors"
      Namespace: !Sub "${AWS::StackName}-log-errors"
      MetricName: Errors
      Statistic: Maximum
      ComparisonOperator: GreaterThanThreshold
      Period: 1 # 1 minute
      EvaluationPeriods: 1
      Threshold: 0
      TreatMissingData: notBreaching
      ActionsEnabled: yes 
```

现在这个警报可以作为变更集执行时的回滚触发器:

```
ALARM_ARN=$1

ROLLBACK_TRIGGER=$(cat <<EOF {
  "RollbackTriggers": [
    {
      "Arn": "$ALARM_ARN",
      "Type": "AWS::CloudWatch::Alarm"
    }
  ],
  "MonitoringTimeInMinutes": 1
} EOF )

aws cloudformation create-change-set \
    --change-set-name "$CHANGE_SET_NAME" \
    --stack-name "$STACK_NAME" \
    --template-body "$TPL_PATH" \
    --change-set-type "UPDATE" \
    --rollback-configuration "$ROLLBACK_TRIGGER" 
```

## 第 5 课:确保您部署的是最新版本的模板

很容易部署不是最新版本的 cloudformation 模板，并造成大量损害。事实上，这种情况发生在我们的团队中:开发人员没有从 git 中提取最新的变更，并且不知不觉地部署了先前版本的堆栈。这导致使用该堆栈的应用程序出现一些停机时间。

一些简单的事情，比如在执行部署之前检查您的分支是否是最新的，就可以了(假设 git 是您的版本控制工具):

```
git fetch
HEADHASH=$(git rev-parse HEAD)
UPSTREAMHASH=$(git rev-parse master@{upstream})

if [[ "$HEADHASH" != "$UPSTREAMHASH" ]] ; then echo "Branch is not up to date with origin. Aborting"
   exit 1
fi 
```

## 第六课:不要多此一举

看起来部署云编队很简单。您只需要一堆执行 aws cli 命令的 bash 脚本。

[![Well yes, but actually no](img/3ab2233b8b5b3074a4c001d1f9501d15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s3Hwh6TN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kyyfqsq80sy159b2vgin.jpg)

四年前，我从一个叫做`aws cloudformation create-stack` command 的简单脚本开始。没过多久，剧本就不再简单了。学到的每一课都让剧本变得越来越复杂。它不仅复杂，而且漏洞百出。我很久以后才注意到，当输出太大时，呈现变更集更改的命令不会返回完整的更改集。

我在一个相对较小的 IT 部门工作。事实证明——这并不奇怪——每个团队都发明了自己部署 cloudformation 堆栈的方式。绝对是坏事。我们可以做得更好，而不是独立地重新发明同样的东西。但是这已经发生了，而且很可能也发生在其他车队。现在，我们正在迁移到统一的部署方式，这比我最初想象的要花更多的精力。我希望我们的团队从一开始就同意一个部署工具。

幸运的是，有很多工具可以帮助部署和配置 cloudformation 堆栈。事实上，我自己也写了一个这样的工具。它叫做 [stack-assembly](https://github.com/molecule-man/stack-assembly) ，它是在牢记所有提到的经验的基础上编写的。