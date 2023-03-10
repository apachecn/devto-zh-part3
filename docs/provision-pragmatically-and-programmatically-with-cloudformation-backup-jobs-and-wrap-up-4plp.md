# 使用 CloudFormation 以实用和编程的方式进行调配:第 3 部分，备份作业和总结

> 原文：<https://dev.to/cmiles74/provision-pragmatically-and-programmatically-with-cloudformation-backup-jobs-and-wrap-up-4plp>

[![CloudFormation Diagram](img/4aece22eb3c23847d49eb6953931d660.png "Diagram of our CloudFormation Tutorial Stack")](https://res.cloudinary.com/practicaldev/image/fetch/s--li3Odp1m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/cmiles74/cloudformation-tutorial/master/template-diagram-3.png)

这是三部分系列的第三部分，也是最后一部分，在这个系列中，我们为我认为非常典型的环境构建了一个云信息模板:一个虚拟私有云分成两个子网，包含两个实例。到目前为止，我们已经涵盖了...

*   [第 1 部分:我们设置 VPC 和子网](https://dev.to/cmiles74/provision-pragmatically-and-programmatically-with-cloudformation-3gni)
*   [第 2 部分:我们设置一些桶、组和特权，以及我们的实例](https://dev.to/cmiles74/provision-programmatically-with-cloudformation-finally-some-instances-2dha)

在这最后一部分，我们将备份作业放置到位，并完成模板。我们已经介绍了很多云形成语法，并且已经真正使用了这个工具，在本系列的最后一部分之后，我们将会有你需要的一切来支持你的下一个项目。如果你想浏览完整的模板，[可以在 GitHub](https://github.com/cmiles74/cloudformation-tutorial) 上找到。

## 为快照(和保留)设置 Lambda

备份实例和数据的内容、时间和方式是一个复杂的主题，涵盖了很多方面。对于这个项目，我们将只处理最低限度的:执行图像的夜间快照，并根据计划保留这些快照。这将在备份时获得服务器存储的可靠映像，但可能无法保证数据库文件处于一致的状态。这里我们不介绍这个过程，但是我的建议是在您的数据库服务器上设置一个作业，在快照作业运行之前将数据库的备份写入磁盘。如果您曾经恢复过快照，您将会有一个一致的数据库备份。

我们将使用 [Amazon Lambda](https://aws.amazon.com/lambda/) 服务来执行快照并剔除旧的快照图像。由于 Lambda 脚本独立于我们的实例运行，因此我们可以确保它总是按时运行，并且我们可以监控备份作业本身的成功或失败:我们不需要提供对实例的管理访问来执行或监控备份过程。我们还可以编写备份作业，并从我们的 CloudFormation 脚本中开始运行它，这真的很棒。

### 每日快照作业

我们需要做的第一件事是为我们的备份作业设置一个新角色，该角色将能够管理快照并将日志写入云信息(以便我们可以监控这些备份作业)。

```
 TutorialLambdaBackupRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Statement:
          - Effect: Allow
            Principal:
              Service: lambda.amazonaws.com
            Action:
              - sts:AssumeRole
      Policies:
        - PolicyName: TutorialLambdaBackupRolePolicy
          PolicyDocument:
           Statement:
             - Effect: Allow
               Action:
                 - ec2:CreateTags
                 - ec2:CreateSnapshot
                 - ec2:DeleteSnapshot
                 - ec2:Describe*
                 - ec2:ModifySnapshotAttribute
                 - ec2:ResetSnapshotAttribute
                 - xray:PutTraceSegments
                 - xray:PutTelemetryRecords
                 - xray:GetSamplingRules
                 - xray:GetSamplingTargets
                 - xray:GetSamplingStatisticSummaries
                 - logs:*
               Resource: "*" 
```

Enter fullscreen mode Exit fullscreen mode

我们以前见过所有这些东西:我们创建一个新角色，我们将 AWS 服务(在这个例子中是 Lambda)和`AssumeRole`动作关联起来，让我们分配角色的任何人都能够“承担”它。然后，我们为该角色创建一个新策略，允许担任该角色的任何人访问管理我们帐户的快照，以及写入云形成日志和[亚马逊 X 射线(跟踪)服务](https://aws.amazon.com/xray/)的能力。

接下来我们创建我们的 Lambda 进程，我将暂时忽略代码，我们将在下一步讨论。将备份脚本放入 Lambda 有更好的方法，但是它们都增加了本文的复杂性。当你有时间和精力的时候，你可以自己研究这个问题(或者我会写另一篇文章)。无论如何，现在让脚本内联是可行的。

```
 TutorialCreateBackupLambda:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: ebs-snapshots-create
      Description: Create EBS Snapshots
      Handler: index.handler
      Role: !GetAtt TutorialLambdaBackupRole.Arn
      Environment:
        Variables:
          REGIONS: !Ref AWS::Region
      Runtime: python3.6
      Timeout: 90
      TracingConfig:
        Mode: Active
      Code:
        ZipFile: |
        ...
        code ellided for clarity
        ... 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`AWS::Lambda::Function`资源来提供我们新的 Lambda 函数，我们给它分配一个名称、描述，并告诉它如何调用自己。我们将使用 [Python](https://www.python.org/) 编写备份作业，因为这样可以保持代码简单，而且几乎每个人都知道 Python。`;-`接下来，Python 代码本身。

```
import os
from datetime import datetime, timedelta
import boto3

def handler(event, context):

  ec2_client = boto3.client("ec2")
  env_regions = os.getenv("REGIONS", None)
  today = datetime.today()
  total_created = 0

  if env_regions:
    regions = ec2_client.describe_regions(RegionNames = env_regions.split(","))
  else:
    return total_created

  # loop through all of our regions
  for region in regions.get("Regions", []):
    regionName = region["RegionName"]
    print("Checking for volumes in region %s" % (regionName))
    ec2_client = boto3.client("ec2", region_name = region["RegionName"])

    # query for volumes with matching tags that are in use
    result = ec2_client.describe_volumes(Filters = [
      {"Name": "status", "Values": ["in-use"]}
    ])

    if not result:
      print("No matching EBS volumes with tag 'Client' = '%s' and tag 'Project' = '%s' in region %s" % (clientTag, projectTag, regionName))
      return total_created

    for volume in result["Volumes"]:
      volume_name = volume["VolumeId"]
      volume_description = "Created by ebs-snapshots-create"

      # check the volume for matching tags
      if "Tags" in volume:
        for tag in volume["Tags"]:
          if tag["Key"] == "Name":
            volume_name = tag["Value"]
          elif tag["Key"] == "Description":
            volume_description = tag["Value"]

      # daily
      print("Creating snapshot for EBS volume %s" % (volume["VolumeId"]))
      result = ec2_client.create_snapshot(
        VolumeId = volume["VolumeId"],
        Description = volume_description
      )
      ec2_resource = boto3.resource("ec2", region_name = region["RegionName"])
      snapshot_resource = ec2_resource.Snapshot(result["SnapshotId"])
      snapshot_resource.create_tags(Tags = [
        {"Key": "Name", "Value": volume_name},
        {"Key": "Description", "Value": volume_description},
        {"Key": "Interval", "Value": "Daily"}
      ])
      total_created += 1

      # quarterly
      if today.day == 1 and today.month % 4 == 0:
        print("Creating quarterly snapshot for EBS volume %s" % (volume["VolumeId"]))
        result = ec2_client.create_snapshot(
        VolumeId = volume["VolumeId"],
        Description = volume_description
        )
        ec2_resource = boto3.resource("ec2", region_name = region["RegionName"])
        snapshot_resource = ec2_resource.Snapshot(result["SnapshotId"])
        snapshot_resource.create_tags(Tags = [
          {"Key": "Name", "Value": volume_name},
          {"Key": "Description", "Value": volume_description},
          {"Key": "Interval", "Value": "Quarterly"}
        ])
        total_created += 1

      # annual
      if today.day == 31 and today.month == 12:
        print("Creating annual snapshot for EBS volume %s" % (volume["VolumeId"]))
        result = ec2_client.create_snapshot(
        VolumeId = volume["VolumeId"],
        Description = volume_description
        )
        ec2_resource = boto3.resource("ec2", region_name = region["RegionName"])
        snapshot_resource = ec2_resource.Snapshot(result["SnapshotId"])
        snapshot_resource.create_tags(Tags = [
          {"Key": "Name", "Value": volume_name},
          {"Key": "Description", "Value": volume_description},
          {"Key": "Interval", "Value": "Annual"},
        ])
        total_created += 1

  return total_created 
```

Enter fullscreen mode Exit fullscreen mode

内联 Lambda 脚本只有这么多字符可用，所以我尽量减少注释。脚本很简单，代码(为了清晰起见)没有做任何复杂的事情；这应该很容易理解。基本上，它所做的是检查我们在`REGIONS`环境变量中提供的所有区域，然后检查该区域的实例。当它找到一个卷时，它会获得所有卷的句柄，然后给每个卷拍快照。

*   首先创建每日快照，脚本总是获取此快照
*   如果这是该季度第一个月的第一天，那么也会拍摄季度快照
*   如果是一年的最后一天，我们就拍摄年度快照

对于创建的每个快照，我们的脚本将快照名称设置为与卷名和“由 ebs-snapshot-create 创建”的描述(我们的 Lambda 函数的名称)相匹配。它还在每个快照上设置“Client”和“Project”标记，以匹配环境变量值。

该脚本跟踪它创建了多少个快照，并在函数结束时返回该数字。在这个过程中，我们还`print`将数据输出到标准输出:当脚本运行时，标准输出和返回值都将被收集和记录。

### 每日快照保留作业

我们不想无限期地累积快照，别忘了我们要为它们的存储付费。下一份工作将加强我们的保留政策。

```
 TutorialDeleteBackupLambda:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: ebs-snapshots-delete
      Description: Delete Old EBS Snapshots
      Handler: index.handler
      Role: !GetAtt TutorialLambdaBackupRole.Arn
      Environment:
        Variables:
          REGIONS: !Ref AWS::Region
      Runtime: python3.6
      Timeout: 90
      TracingConfig:
        Mode: Active
      Code:
        ZipFile: |
        ...
        code elided for clarity
        ... 
```

Enter fullscreen mode Exit fullscreen mode

除了它的名字和描述之外，它几乎和上一份工作一模一样。真正的区别在于 Lambda 函数的脚本。该脚本计算我们每日快照的截止日期:七天。当脚本检查卷的快照时，它将检查我们在备份作业中放置在快照上的标记，特别是“Interval”标记。早于我们截止日期的每日快照将被删除，仅留下最近七个月的每日快照。

我们对四分之一的快照做同样的事情；我们计算截止数据，然后检查所有标记有“Interval”值“Quarterly”的快照。所有超过截止时间的快照都被删除，只剩下四个最近的四分之一快照。

我们留下的年度快照将永远以每年一次的速度累积。你永远不知道什么时候会有人想要挖掘旧的历史数据。

```
import os
from datetime import datetime, timedelta
from dateutil import relativedelta
import boto3

def handler(event, context):

  ec2_client = boto3.client("ec2")
  date_format = "%Y-%m-%dT%H:%M:%S.%fZ"
  daily_cutoff = (datetime.utcnow() - timedelta(days = 7)).strftime(date_format)
  env_regions = os.getenv("REGIONS", None)
  today = datetime.today()
  total_deleted = 0

  if env_regions:
    regions = ec2_client.describe_regions(RegionNames = env_regions.split(","))
  else:
    return total_created

  for region in regions.get("Regions", []):
    regionName = region["RegionName"]
    print("Checking for snapshots in region %s" % (regionName))

    # query for Daily snapshots with matching tags
    result = ec2_client.describe_snapshots(Filters = [
      {"Name": "tag:Interval", "Values": ["Daily"]}
    ])

    if not result:
      print("No matching Daily snapshots with tag 'Client' = '%s' and tag 'Project' = '%s' in region %s" % (clientTag, projectTag, regionName))
      return total_deleted

    for snapshot in result["Snapshots"]:
      snapshot_time = snapshot["StartTime"].strftime(date_format)
      snapshot_id = snapshot["SnapshotId"]

      if daily_cutoff > snapshot_time:
        snapshot_name = ""
        if "Tags" in snapshot:
          for tag in snapshot["Tags"]:
              if tag["Key"] == "Name":
                  snapshot_name = tag["Value"]

        print("Deleting Daily EBS snapshot ID = %s, Name = %s" % (snapshot_id, snapshot_name))
        ec2_client.delete_snapshot(SnapshotId = snapshot_id)
        total_deleted += 1

    # query for quarterly snapshots with matching tags
    if today.day == 1 and today.month % 4 == 1:
      quarterly_cutoff_raw = datetime.utcnow() - relativedelta.relativedelta(months = 15) # keep five quarters
      quarterly_cutoff = quarterly_cutoff_raw.strftime(date_format)
      result = ec2_client.describe_snapshots(Filters = [
        {"Name": "tag:Interval", "Values": ["Quarterly"]}
      ])

      if not result:
        print("No matching Quarterly snapshots with tag 'Client' = '%s' and tag 'Project' = '%s' in region %s" % (clientTag, projectTag, regionName))
        return total_deleted

      for snapshot in result["Snapshots"]:
        snapshot_time = snapshot["StartTime"].strftime(date_format)
        snapshot_id = snapshot["SnapshotId"]

        if quarterly_cutoff > snapshot_time:
          snapshot_name = ""
          if "Tags" in snapshot:
            for tag in snapshot["Tags"]:
                if tag["Key"] == "Name":
                    snapshot_name = tag["Value"]

          print("Deleting Quarterly EBS snapshot ID = %s, Name = %s" % (snapshot_id, snapshot_name))
          ec2_client.delete_snapshot(SnapshotId = snapshot_id)
          total_deleted += 1

  return total_deleted 
```

Enter fullscreen mode Exit fullscreen mode

将这两个函数添加到我们的 CloudFormation 模板中后，我们就可以开始了。我们需要做的就是每天晚上给这些工作打电话。

### 规则来调用我们的作业

我们希望每天晚上都运行作业，首先是备份作业，然后是剔除旧快照的保留作业。幸运的是，CloudWatch 将允许您创建一个按计划触发的[“规则”](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/Create-CloudWatch-Events-Scheduled-Rule.html)，我们可以创建自己的规则来启动我们的备份作业。可以使用 [`AWS::Events::Rule`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-events-rule-target.html) 资源来做到这一点。

```
 TutorialPerformBackupRule:
    Type: AWS::Events::Rule
    Properties:
      Description: Triggers our backup lambda script
      ScheduleExpression: cron(30 4 * * ? *)
      State: ENABLED
      Targets:
        - Arn: !GetAtt TutorialCreateBackupLambda.Arn
          Id: TutorialBackupRule

  TutorialDeleteBackupRule:
    Type: AWS::Events::Rule
    Properties:
      Description: Triggers our backup delete lambda script
      ScheduleExpression: cron(45 4 * * ? *)
      State: ENABLED
      Targets:
        - Arn: !GetAtt TutorialDeleteBackupLambda.Arn
          Id: TutorialBackupRule 
```

Enter fullscreen mode Exit fullscreen mode

这两个作业使用熟悉的 [cron 风格语法](https://crontab.guru/)，我们在每天凌晨 4:00 备份，然后在凌晨 4:45 删除旧快照。每个规则都有一个指向它将调用的 Lambda 函数的`ARN`的指针。

设置好我们的作业后，我们需要为我们的规则提供使用 [`AWS::Lambda::Permission`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-lambda-permission.html) 资源调用 Lambda 函数的权限。

```
 TutorialPermissionCreate:
    Type: AWS::Lambda::Permission
    Properties:
      FunctionName: !GetAtt TutorialCreateBackupLambda.Arn
      Action: lambda:InvokeFunction
      Principal: events.amazonaws.com
      SourceArn: !GetAtt TutorialPerformBackupRule.Arn

  TutorialPermissionDelete:
    Type: AWS::Lambda::Permission
    Properties:
      FunctionName: !GetAtt TutorialDeleteBackupLambda.Arn
      Action: lambda:InvokeFunction
      Principal: events.amazonaws.com
      SourceArn: !GetAtt TutorialDeleteBackupRule.Arn 
```

Enter fullscreen mode Exit fullscreen mode

我们向两个 CloudFront 触发器添加了`InvokeFunction`权限，这样它们就可以调用我们的 Lambda 函数。有了它，我们的备份就都搞定了。

## 模板总结:输出

任何 CloudFront 模板的最后一点是提供输出数据，这些数据将作为 JSON 对象提供。您可以将这些数据提供给另一个进程，该进程会再次检查资源是否已创建或执行其他任务。这是我们的输出部分...

```
Outputs:
  WebServer:
    Description: A reference to the web server
    Value: !Ref TutorialWebServer

  DatabaseServer:
    Description: A reference to the database server
    Value: !Ref TutorialDatabaseServer

  BackupS3Bucket:
    Description: S3 Bucket used to backup data
    Value: !Sub |
      https://${TutorialBackupS3Bucket.DomainName}\ 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们返回对两个服务器实例和 S3 桶的引用。在这一部分，我们当然可以列出更多的项目，也许是我们的 VPC。现在我们将让它简短扼要。

咻！这是这个系列的最后一个作品！您可以根据我们在此处整理的模板进行建模，并开始以稍微更快的速度和信心在 CloudFormation 上配置资源。`;-D`

* * *