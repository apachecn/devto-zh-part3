# AWS DevOps Pro 认证博文系列:AWS Lambda

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-aws-lambda-5ghb>

Rhii 摄影公司在 [Unsplash](https://unsplash.com/photos/Xy6FpnFyVjo) 上拍摄的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我试图完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以请确保定期重温博客帖子。

## 什么？

AWS Lambda 是...

*   无服务器功能框架
*   与 AWS 服务高度集成
*   非常适合开发运维任务

其他资源:

*   [AWS Lambda 用户指南](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html?sc_ichannel=ha&sc_icampaign=pa_lamdbaresourcestop&sc_icontent=devguide&sc_detail=1)
*   [AWS Lambda 常见问题解答](https://aws.amazon.com/lambda/faqs/)
*   [AWS Lambda API](https://docs.aws.amazon.com/lambda/latest/dg/API_Reference.html)
*   [AWS Lambda CLI](https://docs.aws.amazon.com/cli/latest/reference/lambda/index.html)

## 为什么？

下面是一些 DevOps / Infra 团队可能使用 AWS Lambda 的案例的想法。这些都不是新的或突破性的创新。唯一的区别是，当试图在 AWS Lambda 中实现这些时，我们不再需要考虑新的服务器，计费是按秒进行的，Lambda 是为了与其他 AWS 服务进行交流而构建的。

*   通过 EBS 快照自动备份/循环
*   生成报告——用它来审计 AWS 上的资源(如果您不想在 AWS 配置上花钱)
*   执行 S3 操作，即将代码构建工件移动到给定 S3 桶上的某个位置
*   批处理日志——从各种资源中提取、转换和加载(ETL ),并整合到一个中央数据仓库中
*   预定任务——按照给定的时间表执行上述任何用例，就像 cron 作业一样
*   ChatOps -运行 Slack 聊天机器人来管理和报告您的基础设施

受 epagon 博客文章启发的用例:[为什么 DevOps 工程师喜欢 AWS](https://epsagon.com/blog/why-devops-engineers-love-aws-lambda/)

## 什么时候？

*   您需要执行一项 DevOps 任务，但不想设置一个应用服务器来托管它。
*   您试图打破负责调度和运行 DevOps 任务的单一管理服务器。

## 如何？

我们将使用一个简单的例子，其中 DevOps 工程师希望记录给定 S3 存储桶上传的所有文件。围绕本教程的大量艰苦工作是通过 Sunil Dalal 的博客文章“[使用 Lambda 作为 S3 事件处理器](https://www.polyglotdeveloper.com/lambda/2017-07-05-Using-Lambda-as-S3-events-processor/)”完成的。谢谢 Sunil 的分享！

### 先决条件

*   创建 lambda 执行角色，以授予 Lambda 对服务和资源的访问权限。这可以通过控制台使用[导轨](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)来完成。复制 ARN 你会需要它，当我们上传功能。该角色应应用以下权限:
    *   允许角色创建 CloudWatch 日志条目。
    *   允许角色 S3 只读访问
*   您已经有了一个 S3 桶(为这个演练创建一个可能是个好主意)
*   安装以下工具:
    *   jq 用于从 AWS CLI 获取 JSON 数据
    *   [awslogs](https://github.com/jorgebastida/awslogs) 如果想在终端查看 CloudWatch 日志条目)
*   AWS CLI 默认配置文件已配置
    *   要输出 JSON
    *   具有固定的区域

### 创建文件

复制下面的代码片段，并将其命名为`index.js`

```
exports.handler = async (event) => {
    var srcBucket = event.Records[0].s3.bucket.name;
    var srcKey = decodeURIComponent(event.Records[0].s3.object.key);

    console.log("bucket:", srcBucket, " file: ", srcKey);
}; 
```

复制下面的代码片段，并将其命名为`payload-test.json`

```
{  "Records":[  {  "eventVersion":"2.0",  "eventSource":"aws:s3",  "awsRegion":"us-west-2",  "eventTime":"1970-01-01T00:00:00.000Z",  "eventName":"ObjectCreated:Put",  "userIdentity":{  "principalId":"AIDAJDPLRKLG7UEXAMPLE"  },  "requestParameters":{  "sourceIPAddress":"127.0.0.1"  },  "responseElements":{  "x-amz-request-id":"C3D13FE58DE4C810",  "x-amz-id-2":"FMyUVURIY8/IgAtTv8xRjskZQpcIZ9KG4V5Wp6S7S/JRWeUWerMUE5JgHvANOjpD"  },  "s3":{  "s3SchemaVersion":"1.0",  "configurationId":"testConfigRule",  "bucket":{  "name":"sourcebucket",  "ownerIdentity":{  "principalId":"A3NL1KOZZKExample"  },  "arn":"arn:aws:s3:::sourcebucket"  },  "object":{  "key":"HappyFace.jpg",  "size":1024,  "eTag":"d41d8cd98f00b204e9800998ecf8427e",  "versionId":"096fKKXTRTtl3on89fVO.nfljtsv6qko"  }  }  }  ]  } 
```

会话的其余部分可以通过命令行完成:

```
export LAMBDA_NAME=your-lambda-function-name
export LAMBDA_ROLE=your-lambda-execution-role
export AWS_ACCOUNT_ID=$(aws sts get-caller-identity | jq -r ".Account")
export LAMBDA_ROLE_ARN=arn:aws:iam::$AWS_ACCOUNT_ID:role/$LAMBDA_ROLE
export LAMBDA_S3_BUCKET=your-s3-bucket
export LAMBDA_S3_ARN=arn:aws:s3:::$LAMBDA_S3_BUCKET

# package the lambda function
zip function.zip index.js

# create function
aws lambda create-function --function-name $LAMBDA_NAME \
  --zip-file fileb://function.zip \
  --handler index.handler \
  --runtime nodejs10.x \
  --role $LAMBDA_ROLE_ARN

# setup S3 notifications, first we'll allow the s3 bucket to invoke the lambda
aws lambda add-permission \
  --function-name $LAMBDA_NAME \
  --principal s3.amazonaws.com \
  --statement-id $LAMBDA_NAME$RANDOM \
  --action "lambda:InvokeFunction" \
  --source-arn $LAMBDA_S3_ARN \
  --source-account $AWS_ACCOUNT_ID

export LAMBDA_ARN=$(aws lambda get-function --function-name $LAMBDA_NAME  | jq -r .Configuration.FunctionArn)

cat << EOF > notification.json
{
    "LambdaFunctionConfigurations": [
      {
        "Id": "1234567890",
        "LambdaFunctionArn": "$LAMBDA_ARN",
        "Events": [ "s3:ObjectCreated:*" ]
      }
    ]
} EOF # next create the notification event in the bucket
# DANGER: this will overwrite any existing event notifications in your bucket
# DO NOT RUN THIS on a bucket that is important to you or work!
aws s3api put-bucket-notification-configuration \
  --bucket $LAMBDA_S3_BUCKET \
  --notification-configuration file://notification.json

# test the integration to see if the message formatting is correct. 
# this should look identical to the actual CloudWatch entry.
# n.b. base64 appears to use the same switch to decode for both BSD and GNU 
# variants
aws lambda invoke \
  --invocation-type RequestResponse \
  --function-name $LAMBDA_NAME \
  --log-type Tail \
  --payload file://payload-test.json outputfile.txt \
  | jq -r .LogResult | base64 --decode

# Finally let's test it properly
touch hello.txt
aws s3 cp hello.txt s3://$LAMBDA_S3_BUCKET/

# If you didn't install awslogs, you can still use AWS Console to view logs in 
# Cloud Watch.
awslogs get /aws/lambda/s3-blab --start='5 min'

# Teardown

aws lambda delete-function --function-name $LAMBDA_NAME

aws s3api put-bucket-notification-configuration \
  --bucket $LAMBDA_S3_BUCKET \
  --notification-configuration 'LambdaFunctionConfigurations=[]'

# you may wish to clear down the S3 bucket of your test files and the log group 
# that was created in CloudWatch. 
```

## API 和 CLI 特性和动词

**特性**

*   别名
*   事件源映射
*   功能

**动词(CRUD)**

*   创造
*   获取/列表
*   更新(功能-[代码/配置])
*   删除

**离群值**

*   添加图层版本权限
*   添加权限
*   删除-功能-并发
*   删除-图层-版本
*   获取-帐户-设置
*   获取功能配置
*   获取图层版本
*   通过 arn 获取图层版本
*   获取图层版本策略
*   获取策略
*   引起
*   列表层版本
*   列表层
*   列表标签
*   按功能列出版本
*   发布-图层-版本
*   发布版本
*   放入功能并发
*   移除图层版本权限
*   移除权限
*   标签资源
*   UNTAG-资源
*   等待

Unsplash path(我用来获得封面图片的术语):lambda(你必须试试)，magic，harry potter(与 magic 相关的标签)

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*