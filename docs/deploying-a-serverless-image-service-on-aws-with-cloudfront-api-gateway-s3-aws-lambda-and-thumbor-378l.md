# 使用 CloudFront、Api Gateway、S3、AWS Lambda 和 Thumbor 在 AWS 上部署无服务器影像服务

> 原文：<https://dev.to/astuyve/deploying-a-serverless-image-service-on-aws-with-cloudfront-api-gateway-s3-aws-lambda-and-thumbor-378l>

作为青年运动技术的大熔炉， [SportsEngine](https://sportsengine.com) 经历了相当多的整合挑战。聚合和整合球员信息是我们公司长期成功的关键，其中很大一部分是处理平台上运动员的图像。

传统上，裁剪、调整和存储图像的解决方案很简单。你可以决定你需要多少尺寸和格式的图片，当用户决定在他们的个人资料中添加新图片时，你可以将每个版本上传到 S3。在 Ruby on Rails 的世界中，我会使用类似 CarrierWave 的东西与 S3 接口，在 API 中公开结果图像，然后就完成了。这在一段时间内效果很好。

今天的用户希望他们的桌面、移动和 web 应用程序体验有高分辨率的图像。为每一次上传创建几十个不同版本的图像是不省时的。相反，最好设计一个系统，允许客户机指定图像大小、文件类型和色调等细节，并让服务创建一个图像作为响应。

输入影像服务。AWS 用[无服务器图像处理程序模板](https://aws.amazon.com/answers/web-applications/serverless-image-handler/)使它变得简单。使用 CloudFront、Api Gateway、Lambda 和 S3，您可以轻松地部署一个新的服务来动态操作图像。您甚至可以利用 AWS Rekognition 来自动检测人脸并裁剪图像以使主题居中！

### 无服务器图像处理器架构

[![Serverless Image Handler Architecture Diagram](img/af2f8dfd227b836a5480305257b97214.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TViwCLPk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aaronstuyvenberg.com/asseimg/serverless_image_handler.png)T4】

这个过程是这样的:

1.  客户端从 CloudFront URL 请求一个图像。
2.  CloudFront 检查它的缓存。如果图像存在，它将被返回。
3.  如果图像不存在，客户机请求被发送到 API 网关处理的资源
4.  API 网关触发 AWS Lambda 函数，该函数:
5.  从亚马逊 S3 获取现有图像
6.  使用拇指操作图像
7.  将图像存储在 S3
8.  将图像返回给 API 网关
9.  然后，API Gateway 将生成的图像存储在 CloudFront 中，该图像现在被缓存起来，以便将来更快地访问。

这远远优于传统 web 服务的工作方式，传统 web 服务基本上预先生成图像的所有所需版本。如果你需要一个新的，你会面临一个讨厌的，长时间运行的任务来转换所有现有的图像-不管你是否需要每一个图像，或者只是几千个！然而，最初的无服务器图像处理器缺少一些关键特性。

*   首先，它只支持一个开箱即用的 S3 桶
*   其次，该实现要求在查询字符串中指定所有图像参数。这在内部工作得很好，但是向最终用户暴露了太多的信息。

### 适应扩展服务

以下是构建和部署更加灵活、强大、无需服务器的影像服务的简单方法。

首先，只需省略 AWS_LOADER_bucket，然后在 URL 中将 BUCKET 名称作为第一个参数传递给 Thumbor，就可以启用多 BUCKET 支持

*   在 CloudFormation 模板中，将 [AWS_LOADER_BUCKET](https://github.com/awslabs/serverless-image-handler/blob/f47c7c7c8a29e605921297a4bf301a24637f10b1/deployment/serverless-image-handler.template#L366) 环境变量设置为空字符串(“”)。

```
 Environment:
        Variables:
          TC_AWS_LOADER_BUCKET: '' # MUST be an empty string, cannot be null
          TC_AWS_ENDPOINT: !FindInMap
            - S3EndPointMap
            - us-east-1
            - endpoint
          TC_AWS_REGION: us-east-1
          # SO-SIH-155 - 07/16/2018 - Rekognition integration
          # Adding env variable for AWS Rekognition
          REKOGNITION_REGION: us-east-1
          LOG_LEVEL: INFO 
```

*   将桶作为 URL 中的第一个参数传递

`https://${Domain}/fit-in/100x100/image-name.jpg`变成了`https://${Domain}/fit-in/100x100/bucket_name/image-name.jpg`

值得注意的是，默认的 [CloudFormation 模板](https://github.com/awslabs/serverless-image-handler/blob/master/deployment/serverless-image-handler.template#L237)为您的堆栈创建了一个 IAM 策略，以便您可以从指定的桶中读取。如果您计划使用多个存储桶，您应该显式地创建附加策略，或者确保存储桶是公共的。

我想做的下一件事是提供一种别名图像操作组合的方法。我不喜欢在查询字符串中指定所有的`256x256`和`files:format(png)`。相反，我想简单地说`ios_profile_image`或`android_org_logo`，并知道我将得到一个符合预设格式和尺寸的图像。我不想失去即时将图像转换成任意格式的能力，但我确实希望有一个更简洁的 URL 来传递——尤其是对公众消费者。

这部分很简单。无服务器图像处理程序模板已经使用了 AWS API 网关，所以我们简单地添加了一个额外的资源，并将其路由回模板提供的默认 API 网关路由。看起来是这样的:

```
 IosProfileImageMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      ApiKeyRequired: false
      AuthorizationType: NONE
      RestApiId: !Ref ImageHandlerApi
      ResourceId: !Ref IosProfileImage
      HttpMethod: ANY
      RequestParameters:
        method.request.path.imagepath: true
      Integration:
        Type: HTTP_PROXY
        IntegrationHttpMethod: ANY
        PassthroughBehavior: WHEN_NO_MATCH
        RequestParameters:
          integration.request.path.imagepath: method.request.path.imagepath
        Uri: !Sub
          - "https://${ImageHandlerApi}.execute-api.${AWS::Region}.amazonaws.com/image/90x90/smart/filters:format(png)/${Bucket}/{imagepath}"
          - ImageHandlerApi: !Ref ImageHandlerApi
            Bucket: !FindInMap [ PerAccount, !Ref "AWS::AccountId", AssetsBucket ] 
```

此时我们意识到，尽管我们希望支持多个存储桶，但是我们可以很容易地将存储桶映射到我们的 API 别名。`ios_profile_image` API 映射到一个 profile images 桶，而`org_logo` API 映射到一个完全不同的桶(实际上是在另一个 AWS 帐户中)。因为我们不需要用户指定起始桶，所以我们能够从别名中完全删除`Bucket`参数。因此，如果一个图像存在于此:

`https://s3.aws.com/some-bucket/profile_images/123/456/image.jpg`

我可以创建一个 90x90，PNG 的人脸检测图像，只需导航到

`http://my_api_gateway.com/profile-image/ios/profile_images/123/456/image.jpg`

这项服务可以快速廉价地转换我需要的图片——按需转换！

### 投入生产

我们的图像服务还有最后一个问题需要解决。一个 bug 会以某种方式阻止我们的 CloudFront URL 正确调用别名！我们可以通过 API 网关直接调用每个别名，但是不知何故，CloudFront 的加入导致服务抛出 CloudFront 400 错误。我们很困惑。

AWS 支持人员能够快速发现问题。在每个 API 网关部署之前，实际上存在一个*隐式 CloudFront 分布*。我们收到的错误是由 CloudFront 通过不同的 CloudFront 发行版检测到三个重定向引起的，请求被终止以减轻潜在的 DDOS 攻击媒介。

解决办法很简单。我们在 API Gateway simple 前面创建的 CloudFront 发行版需要是一个`Regional`发行版，而不是`Edge-Optimized`。一旦我们做出了这一更改，我们就能够将无服务器映像服务投入生产。

这篇文章最初出现在我的个人网站:[https://aaronstuyvenberg.com/serverless-images/](https://aaronstuyvenberg.com/serverless-images/)