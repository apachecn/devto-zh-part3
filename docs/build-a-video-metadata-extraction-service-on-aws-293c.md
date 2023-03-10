# 在 AWS 上构建视频元数据提取服务

> 原文：<https://dev.to/harrison_codes/build-a-video-metadata-extraction-service-on-aws-293c>

在这篇文章中，我将展示如何使用[无服务器框架](https://serverless.com)构建一个从视频内容中提取元数据的自动化系统。我们将使用 AWS Rekognition 的名人识别功能来处理上传到 S3 存储桶的 mp4 文件，然后将生成的元数据以 JSON 格式存储在 S3 的原始视频旁边。

如果这是你第一次使用无服务器，也许值得先浏览一下 [AWS 快速入门指南](https://serverless.com/framework/docs/providers/aws/guide/quick-start/)。但是，如果你想直接进入，请继续，因为我会在我们进行的过程中介绍一些基础知识。

作为参考，您可以在我的 [Github](https://github.com/twosmalltrees/serverless-video-metadata-generator) 上找到这个演练的完整示例代码。

* * *

### 我们将建造什么

在我们真正开始实现之前，理解我们想要创建的东西会有所帮助。

1.  一个视频文件上传到我们的 S3 桶。
2.  该上传触发了一个 Lambda 函数(extractMetadata ),该函数调用 AWS Rekognition startCelebrityRecognition 端点来开始分析作业。
3.  当分析作业完成时，Rekognition 向 SNS 主题发布成功消息。
4.  SNS 消息触发第二个 Lambda 函数(saveMetadata)，该函数从 Rekognition 检索生成的名人元数据，并将其与 S3 的原始视频一起保存。

[![Metadata Extractor Architecture](img/9677ecd263a3da03f9a0726ee59ca77f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YZ2qoUg_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vza1bm3r3419yywf5036.jpeg)

* * *

### 第一步:基本设置

首先，如果您还没有安装，您需要全局安装无服务器以便运行 CLI 命令。

```
$ npm install -g serverless 
```

接下来，我们将创建一个新的无服务器项目:

```
$ serverless create --template aws-nodejs --path metadata-extractor
$ cd metadata-extractor 
```

注意`--template`和`--path`标志，用于指定无服务器模板类型(在本例中是 aws-nodejs)和项目目录(这也是我们的项目名称)。

此时，如果你进入项目目录，你会看到两个自动生成的文件- `serverless.yml`和`handler.js`。这些是我们创建此服务所需的唯一文件。`serverless.yml`是我们定义和配置服务所需的 AWS 资源的地方，而`handler.js`是我们实现 Lambda 代码的地方。

* * *

### 步骤 2:配置 AWS resources-server less . yml

让我们从`serverless.yml`开始。打开这个文件，你会看到相当多的注释代码。这是作为无服务器中可用的各种配置选项的参考提供的，因此值得通读一遍。完成后，删除所有内容！我们将从头开始。

#### 定义一些自定义属性

首先，将以下内容添加到`serverless.yml` :

```
# serverless.yml

service: metadata-extractor

custom:
  bucketName: your-bucket-name-goes-here
  bucketArn: arn:aws:s3:::${self:custom.bucketName}/*
  snsTopicName: your-sns-topic-name-goes-here
  snsTopicArn: arn:aws:sns:${env:AWS_REGION}:${env:AWS_ACCOUNT_ID}:${self:custom.snsTopicName}
  snsPublishRoleName: snsPublishRole
  snsPublishRoleArn: arn:aws:iam::${env:AWS_ACCOUNT_ID}:role/${self:custom.snsPublishRoleName} 
```

查看上面的内容，您会看到我们已经将服务命名为`metadata-extractor`，并且还定义了许多自定义属性:

*   **bucketName** -上传存储桶的名称。你可能会想给它重新命名。
*   **bucketARN** -上传存储桶的 ARN，用标准 S3 ARN 格式的 bucketName 构造。
*   **snsTopicName**-Rekognition 将用来通知作业完成的 SNS 主题的名称。再一次，根据你的喜好重新命名。
*   **snsTopicArn** -上述 SNS 主题的 Arn，使用 AWS 区域、AWS 帐户 id 和主题名称构建。注意，region 和 account id 是对环境变量的引用。
*   snsPublishRoleName——IAM 角色的名称(我们将在后面定义)，它被传递给 Rekognition 以允许向我们的 SNS 主题发布通知。
*   **snspublishlorlearn**-上面命名的角色的 Arn。

使用语法`${self:custom.someVariableName}`,我们能够在 serverless.yml 文件中的其他地方引用这些属性。

#### 设置环境变量并扩展 Lambda IAM 角色

还在`serverless.yml`工作，补充以下:

```
# serverless.yml, continued...

provider:
  name: aws
  runtime: nodejs6.10
  environment:
    SNS_PUBLISH_ROLE_ARN: ${self:custom.snsPublishRoleArn}
    SNS_TOPIC_ARN: ${self:custom.snsTopicArn}
  iamRoleStatements:
    - Effect: Allow
      Action: 
        - rekognition:StartCelebrityRecognition
        - rekognition:GetCelebrityRecognition
      Resource: '*'
    - Effect: Allow
      Action:
        - iam:GetRole
        - iam:PassRole
      Resource: ${self:custom.snsPublishRoleArn}
    - Effect: Allow
      Action:
        - s3:GetObject
        - s3:PutObject
      Resource: ${self:custom.bucketArn} 
```

这里我们添加提供者配置。这包括指定云服务提供商(aws)、运行时(nodejs6.10)。我们还定义了两个在 Lambda 运行时可用的环境变量——SNS 发布角色 ARN 和 SNS 主题 ARN。这些是通过引用我们之前定义的自定义属性来定义的。

此外，我们扩展了 Lambda 函数的默认 IAM 角色，允许启动和获取 Rekognition 作业的结果，获取 SNS 发布角色并将其传递给 Rekognition，以及从我们的 S3 桶中获取对象并将其放入其中。

#### 定义 Lambdas 和事件源

接下来，你会看到我们已经定义了前面提到的两个函数- `extractMetadata`和`saveMetadata` :

```
# serverless.yml, continued...

functions:
  extractMetadata:
    handler: handler.extractMetadata
    events:
      - s3: 
          bucket: ${self:custom.bucketName}
          event: s3:ObjectCreated:*
          rules:
            - suffix: .mp4
  saveMetadata:
    handler: handler.saveMetadata
    events: 
      - sns: ${self:custom.snsTopicName} 
```

对于`extractMetadata`，我们通过 handler 属性将其映射到 extractMetadata 处理程序(我们将在 handler.js 中稍后定义该实现)。我们还分配一个事件作为函数的触发器。如前所述，对于 extractMetadata 函数，这是一个到 uploads bucket 的上传(ObjectCreated)。

我们还设置了一条规则，即上传的文件必须以. mp4 结尾才能触发 Lambda 调用——设置这条规则**非常重要**,因为它可以防止在我们保存生成的 JSON 文件时触发 Lambda——这会导致无限循环，以及快速增长的 AWS 账单。

对于`saveMetadata`，我们将其映射到 saveMetadata 处理程序，并添加 SNS 队列作为事件触发器。与 S3 桶一样，无服务器将确保为我们创建 SNS 主题。

#### 定义自定义 IAM 角色，为 SNS 提供重新确认发布权限

在我们继续讨论函数实现之前，还有最后一件事——我们需要在`serverless.yml`的资源部分定义一个定制的 IAM 角色。这是将传递给 AWS Rekognition 的 IAM 角色，为其提供向 SNS 主题发布通知所需的权限。

添加以下内容:

```
# serverless.yml, continued...

resources:
  Resources:
    snsPublishRole:
      Type: AWS::IAM::Role
      Properties:
        RoleName: ${self:custom.snsPublishRoleName}
        AssumeRolePolicyDocument: 
          Version: '2012-10-17'
          Statement: 
            - Effect: Allow
              Principal: 
                Service: 
                  - rekognition.amazonaws.com
              Action: 
                - sts:AssumeRole
        Policies:
          - PolicyName:  snsPublishPolicy
            PolicyDocument: 
              Version: '2012-10-17'
              Statement: 
                - Effect: Allow
                  Action: 
                    - sns:Publish
                  Resource: ${self:custom.snsTopicArn} 
```

* * *

### 步骤 3:Lambda implementation-handler . js

为了完成我们的元数据提取服务，我们需要定义在`serverless.yml`中引用的两个处理函数( *extractMetadata* 和 *saveMetadata* )。

#### 开始元数据提取

让我们从*提取元数据*开始。将以下内容添加到`handler.js` :

```
// handler.js

const AWS = require('aws-sdk');
const rekognition = new AWS.Rekognition();

module.exports.extractMetadata = (event, context, callback) => {
  const bucketName = event.Records[0].s3.bucket.name;
  const objectKey = event.Records[0].s3.object.key;

  const params = {
    Video: {
      S3Object: {
        Bucket: bucketName,
        Name: objectKey
      }
    },
    NotificationChannel: {
      RoleArn: process.env.SNS_PUBLISH_ROLE_ARN,
      SNSTopicArn: process.env.SNS_TOPIC_ARN,
    },
  };

  rekognition.startCelebrityRecognition(params).promise()
    .then((res) => {
      const response = {
        statusCode: 200,
        body: JSON.stringify(res),
      };
      callback(null, response);      
    })
    .catch((err) => {
      callback(err, null);      
    });
}; 
```

在上面的代码中，您将看到我们首先从事件源(S3 上传)中提取 bucketName 和 objectKey。

从这里开始，只需要调用由 AWS Rekognition SDK 提供的`startCelebrityRekognition`。我们还传递一组参数，这些参数标识了要在 S3 分析的视频的位置、要向其发布成功通知的 SNS 主题 ARN，以及发布到指定主题所需的 IAM 角色 ARN。

#### 得到结果并保存到 S3

接下来，我们定义*保存元数据* :

```
// handler.js, continued...

const s3 = new AWS.S3();

module.exports.saveMetadata = (event, context, callback) => {
  const message = JSON.parse(event.Records[0].Sns.Message);
  const jobId = message.JobId;   
  const bucketName = message.Video.S3Bucket;  
  const objectKey = message.Video.S3ObjectName;
  const metadataObjectKey = objectKey + '.people.json';

  const rekognitionParams = {
    JobId: jobId,
  };

  rekognition.getCelebrityRecognition(rekognitionParams).promise()
    .then((res) => {
      const s3Params = {
        Bucket: bucketName,
        Key: metadataObjectKey,
        Body: JSON.stringify(res),
      };
      s3.putObject(s3Params).promise()
        .then((res) => {
          const response = {
            statusCode: 200,
            body: JSON.stringify(res),
          };
          callback(null, response);
        });
    })
    .catch((err) => {
      callback(err, null); 
    });
}; 
```

上面，我们从事件源(SNS 成功通知)中提取了相当多的细节，然后调用 getCelebrityRekognition(传入 Rekognition jobId)，它检索生成的名人识别元数据。然后，我们使用 S3 SDK 将元数据(作为. json 文件)推送到原始视频文件的位置。

* * *

### 包装完毕

此时，服务几乎可以测试了。但是，首先您需要确保您已经部署了！这应该只是运行命令`serverless deploy`的问题。

一旦您的服务成功部署到 AWS，测试它最简单的方法就是打开 S3 控制台，导航到您的 bucket，手动上传一个`.mp4`。

如果一切顺利，您应该很快就会看到生成的。上传的 mp4 旁边的 json 元数据文件。如果 Rekognition 已经完成了它的工作，它应该可以识别视频中出现的任何名人，以及他们出现的匹配时间码。

如果出现问题，在 AWS 控制台中打开 Cloudwatch，并从 Lambda 日志开始调试。还记得你可以在 [Github repo](https://github.com/twosmalltrees/serverless-video-metadata-generator) 上查看完整代码。

* * *

附言:如果你喜欢我发布的内容，一定要在 [Twitter](https://twitter.com/harrison_codes?lang=en) 上关注我😀