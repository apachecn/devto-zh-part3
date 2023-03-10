# 云形成协议:使用！Sub 而不是！加入

> 原文：<https://dev.to/theburningmonk/cloudformation-protip-use-sub-instead-of-join-1ldl>

CloudFormation 支持许多[内在函数](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html)和`Fn::Join`(或`!Join`)通常用于构造参数化的名称和路径。

例如，[无服务器框架](https://serverless.com/framework/)就广泛使用它。快速浏览它生成的云信息，我可以看到`Fn::Join`用于:

*   IAM 策略名称
*   IAM 角色名称
*   IAM 负责人
*   API 网关 URIs
*   资源 ARNs

还有很多。

但是不仅仅是框架大量使用了`Fn::Join`。它们也一直出现在我们自己的代码中。例如，为资源构造 ARN，或者为 API 网关端点构造 URI。

[![](img/67ae2f5ba0c6bc4f8c6aaf9a8272b273.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R2cIIGfh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/05/img_5cd77476f1b2d.png)

[![](img/182479bd30d9b14219166bd455ecfd2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gEjCRlUL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/05/img_5cd774833cfc3.png)

我发现这些很难理解，我今天给你的提示是用`Fn::Sub`(或`!Sub`速记)来代替。

许多人在需要引用伪参数如`AWS::Region`和`AWS::AccountId`时会使用`Fn::Sub`，例如:

```
!Sub 'arn:aws:ec2:${AWS::Region}:${AWS::AccountId}:vpc/${vpc}' 
```

但是`Fn::Sub`也让你提供自己的参数。比如:

```
!Sub
 - 'arn:aws:s3:::${Bucket}/\*'
 - { Bucket: Ref MyBucket } 
```

相对于`Fn::Join`的优势是你可以看到插值字符串的模式。而对于`Fn::Join`，你必须在头脑中构建模式，这需要更多的认知能量。

```
!Join
 - ''
 - - 'arn:aws:s3:::'
   - !Ref MyBucket
   - '/*' 
```

这里有几个并排的比较来说明这一点。

示例 1: IAM 角色名

```
RoleName: # hello-world-dev-{region}-lambdaRole
 !Join
   - '-'
   - - 'hello-world'
     - 'dev'
     - !Ref 'AWS::Region'
     - 'lambdaRole' 
```

带`Fn::Sub` :

```
PolicyName: !Sub 'hello-world-dev-${AWS::Region}-lambdaRole 
```

示例 2: API 网关集成 URI

```
Uri: # arn:{partition}:apigateway:{region}:.../{lambda}/invocations
  !Join
    - ''
    - - 'arn:'
      - Ref: AWS::Partition
      - ':apigateway:'
      - Ref: AWS::Region
      - ':lambda:path/2015-03-31/functions/'
      - !GetAtt 'HelloLambdaFunction.Arn'
      - '/invocations' 
```

带`Fn::Sub` :

```
Uri:
  !Sub
    - 'arn:${AWS::Partition}:apigateway:${AWS::Region}:lambda:path/2015/03/31/functions/${Function}/invocations'
    - { Function: !GetAtt 'HelloLambdaFunction.Arn' } 
```

示例 API 网关的 Lambda 权限

```
SourceArn: # arn:{partition}:execute-api:{region}:.../*/*
  !Join:
    - ''
    - - 'arn:'
      - Ref: AWS::Partition
      - ':execute-api:'
      - Ref: AWS::Region
      - ':'
      - Ref: AWS::AccountId
      - ':'
      - Ref: ApiGatewayRestApi
      - '/*/*' 
```

带`Fn::Sub` :

```
SourceArn:
  !Sub
    - 'arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${RestApi}/*/*'
    - { RestApi: Ref: ApiGatewayRestApi } 
```

可以说,`Fn::Sub`版本在任何情况下都更容易理解！现在你已经看到了`Fn::Sub`能做什么，我希望你会选择它而不是`Fn::Join`前进。

如果您使用的是无服务器框架，请记住`Fn::Sub`不是本地支持的。这是因为无服务器框架也使用`${}`语法来支持它自己的[变量系统](https://serverless.com/framework/docs/providers/aws/guide/variables/)。然而，正如无服务器框架经常出现的情况一样，您可以使用插件来解决这个问题。查看[无服务器云信息子变量](https://github.com/santiagocardenas/serverless-cloudformation-sub-variables)插件，让你在`serverless.yml`中使用`Fn::Sub`。你只需要用`#{VariableName}`代替`${VariableName}`。

最后，如果你正在使用*无服务器*框架，并且需要比内部函数所能提供的更强的表达能力，那么看看[这个插件](https://dev.to/theburningmonk/introducing-cloudformation-extrinsic-functions-33e1)。它允许你在你的`serverless.yml`中的任何地方使用一些“外在”函数，比如`Fn::Substring`或`Fn::StartsWith`。

帖子[云形成协议:使用！Sub 而不是！加入](https://theburningmonk.com/2019/05/cloudformation-protip-use-fnsub-instead-of-fnjoin/)最早出现在 theburningmonk.com[上](https://theburningmonk.com)。

[![](img/b3069faad4729e2e7fcadab6d656f67a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fvwHVnZW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/theburningmonk/%257E4/Tnt5S1OhlGE)