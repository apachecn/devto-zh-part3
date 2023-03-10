# AWS 云观察日志组保留

> 原文：<https://dev.to/akloya/aws-cloudwatch-log-group-retention-3l47>

Amazon CloudWatch Logs 被用作集中监控、存储和访问来自不同 AWS 服务的所有日志文件的地方

CloudWatch 将日志组织在一个日志组中，当创建一个新的日志组时，它的保留期默认设置为永不过期，这意味着日志将永远保留。

下面是一个示例 python 脚本，它有助于将保留天数更改为 60 天。

```
import boto3
# set the number of retention days 
retention_days = 60
# list the regions you are interested to run this script on
regions=['us-east-1']

for region in regions:
    client = boto3.client('logs',region)
    response = client.describe_log_groups(
    )
    nextToken=response.get('nextToken',None)
    retention = response['logGroups']
    while (nextToken is not None):
        response = client.describe_log_groups(
            nextToken=nextToken
        )
        nextToken = response.get('nextToken', None)
        retention = retention + response['logGroups']
    for group in retention:
        if 'retentionInDays' in group.keys():
            print(group['logGroupName'], group['retentionInDays'],region)
        else:
            print("Retention Not found for ",group['logGroupName'],region)
            setretention = client.put_retention_policy(
                logGroupName=group['logGroupName'],
                retentionInDays=retention_days
                )
            print(setretention) 
```

Enter fullscreen mode Exit fullscreen mode

一旦运行该脚本，现有日志组的问题就解决了，但最好使用云观察事件来自动运行 python 代码，这样以后创建的所有日志组都将设置保留值。