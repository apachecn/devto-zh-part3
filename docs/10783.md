# 使用 AWS CloudFormation 构建 AWS AppSync 管道解析器

> 原文：<https://dev.to/joshuaakahn/building-aws-appsync-pipeline-resolvers-with-aws-cloudformation-1fjg>

在过去几个月里来自 AWS AppSync 的许多更新中，T2 管道解析器 T3 的加入尤其令人兴奋。管道解析器允许我们从一系列可重用的函数中构建一个解析器，每个函数都可以查询一个单独的数据源。这种方法在执行授权检查或解决跨数据源的批处理请求时非常有用。

例如，我最近构建了一个由两个函数组成的管道解析器:

1.  通过 AWS Lambda 数据源从 Amazon elastic cache 中检索最近的商品列表，每个商品都有一个唯一的 ID。
2.  使用 BatchGetItem 从 Amazon DynamoDB 数据源中检索每一项。使用管道解析器允许通过 AppSync 分离功能和组合。

在这篇文章中，我将分享我如何使用*[*AWS cloud formation*](https://aws.amazon.com/cloudformation/)构建解析器，而不是回顾我是如何构建解析器的。如果您对管道解析器的概述或示例实现感兴趣，请参见下面的参考资料部分。

### 用 CloudFormation 构建管道解析器

在 CloudFormation 中构建管道解析器时，我们指定一个解析器以及组成该解析器的函数。解析器及其复合函数将包含请求和响应映射模板，但是只有函数才有数据源。为了演示，我们将实现 Nader Dabit 的[AWS app sync 管道函数简介](https://medium.com/@dabit3/intro-to-aws-appsync-pipeline-functions-3df87ceddac1)中的 ListPosts 解析器:

首先，我们将实现解析器。我们假设已经创建了 AppSync API 和其他适用的资源:

```
ListPostsQueryResolver:  
 Type: AWS::AppSync::Resolver  
 DependsOn: MySchema  
 Properties:
   ApiId: !GetAtt MyApi.ApiId  
   TypeName: Query  
   FieldName: listPosts  
   # configure this resolver as a pipeline  
   Kind: PIPELINE  
   PipelineConfig:  
     Functions:  
       - !GetAtt IsUserCallerFunction.FunctionId  
       - !GetAtt GetPostsFunction.FunctionId  
   # see Nader's article for details on these templates  
   RequestMappingTemplate: |  
     $util.qr($ctx.stash.put("callerId", $ctx.identity.sub))  
     $util.qr($ctx.stash.put("userId", $ctx.args.userId))  
     {}  
   ResponseMappingTemplate: |  
     $util.toJson($ctx.result) 
```

接下来，我们将实现两个管道函数中的第一个:

```
IsUserCallerFunction:  
 Type: AWS::AppSync::FunctionConfiguration  
 Properties:  
   ApiId: !GetAtt MyApi.ApiId  
   Name: is_user_caller  
   Description: Checks to see if the user is also the caller  
   DataSourceName: !GetAtt UsersDynamoDBDataSource.Name  
   FunctionVersion: "2018-05-29"  
   RequestMappingTemplate: |  
     #if($ctx.stash.callerId == $ctx.stash.userId)  
       #return($ctx.prev.result)  
     #else  
     {  
       "operation": "GetItem",  
       "key": {  
         "id": $util.dynamodb.toDynamoDBJson($ctx.stash.callerId),  
       }  
     }  
     #end  
   ResponseMappingTemplate: |  
     #if($ctx.result.level != "admin")  
       $util.error("User is not authorized to make this query")  
     #else  
     $util.toJson($ctx.result)  
     #end 
```

最后，我们将添加第二个函数:

```
GetPostsFunction:  
 Type: AWS::AppSync::FunctionConfiguration  
 Properties:  
   ApiId: !GetAtt MyApi.ApiId  
   Name: get_posts  
   Description: Scan table and retrieve items created by user  
   DataSourceName: !GetAtt PostsDynamoDBDataSource.Name  
   FunctionVersion: "2018-05-29"  
   RequestMappingTemplate: |  
     {  
       "operation" : "Scan",  
       "filter" : {  
         "expression" : "#userId = :userId",  
         "expressionNames" : {  
           "#userId" : "userId"  
         },  
         "expressionValues" : {  
           ":userId" : $util.dynamodb.toDynamoDBJson($ctx.stash.userId)  
         }  
       },  
       "nextToken": $util.toJson($util.defaultIfNullOrBlank($ctx.args.nextToken, null))  
     }  
   ResponseMappingTemplate: |  
     #if($ctx.error)  
       $util.error($ctx.error.message, $ctx.error.type)  
     #end  
     {  
       "items": $util.toJson($ctx.result.items),  
       "nextToken": $util.toJson($util.defaultIfNullOrBlank($context.result.nextToken, null))  
     } 
```

创建 CloudFormation 堆栈后，我们可以通过 AppSync 控制台测试解析器。像往常一样，CloudFormation 带来了重用、可移植性和可配置性的价值。

感谢您的阅读，如果您有任何问题，请随时发表评论。

### 资源

*   [AWS app sync 管道功能介绍](https://medium.com/@dabit3/intro-to-aws-appsync-pipeline-functions-3df87ceddac1)
*   [教程:管道解析器](https://docs.aws.amazon.com/appsync/latest/devguide/tutorial-pipeline-resolvers.html)
*   [AWS AppSync 资源类型参考](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-reference-appsync.html)