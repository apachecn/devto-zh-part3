# 在非键列上查询 AWS DynamoDB

> 原文：<https://dev.to/emiya/querying-aws-dynamodb-on-non-key-columns-44cj>

# 您的客户喜欢 AWS DynamoDB

是的，亚马逊的 NoSQL 数据库服务是一个键值和文档数据库，任何搜索过它的人都应该知道。然而，您的客户要求在没有实际键的非键列上查询 DynamoDB，因为他们可能对项目使用哪些服务有一些限制，或者可能只是没有考虑清楚。

幸运的是，AWS DynamoDB 提供了一种非常快速的方法来实现这种特性:全局二级索引(GSI ),它本质上是对所需的列进行索引，以便您以后可以像查询键一样查询它们。为此，只需通过 DynamoDB 控制台导航到您的表，单击 **Indexes** 选项卡，然后用您想要的键创建索引。

下面是使用 boto 3(Python 的 AWS SDK)查询生成的索引的 Python 示例。

```
import boto3
from boto3.dynamodb.conditions import Key

dynamodb = boto3.resource('dynamodb', endpoint_url='https://your-dynamodb-endpoint')
table = dynamodb.Table('your_table')

results = table.query(
    IndexName='your_index_name',
    KeyConditionExpression=Key('your_index_key').eq('your_index_value')
) 
```

或者，将 Table.scan()与 FilterExpression 一起使用，获得整个表，然后过滤结果，这通常可能比索引更昂贵。