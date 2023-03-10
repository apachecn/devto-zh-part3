# Ruby & AWS Lambda，💖最好的朋友💖

> 原文：<https://dev.to/codegram/ruby-aws-lambda-bff-6mf>

*最初发表于 [Codegram 的博客](https://www.codegram.com/blog/ruby-lambda-bff)T3】*

作为一个 Codegram 的无服务器爱好者，自从 AWS Lambda 引入了对 Ruby 的官方支持以来，是时候发表一篇新的博文了🎉。

你可以在[官方发布的博客文章](https://aws.amazon.com/blogs/compute/announcing-ruby-support-for-aws-lambda/)中阅读更多内容，其中也有一个【Sinatra 应用程序的例子。如果你想对例子和发布进行深入的评论，我推荐[这篇来自 EquiValent 的精彩评论](https://blog.eq8.eu/article/sinatra-on-aws-lambda.html)。

得知我终于可以将 Ruby 与 Lambda 一起使用时，我非常激动，但是用 Sinatra 作为例子感觉是错误的。如果您想用它构建一个 API，您会希望让 API Gateway 完成所有的路由和请求处理。添加一个额外的层来在应用程序级别做同样的事情是不必要的，并且似乎是一种反模式(我知道它被用作迁移微服务的示例，但它仍然令人困惑)。

* * *

在与我的程序员同事讨论后，我决定用 Ruby 和 Lambda 构建一个小型的概念验证 GraphQL API，使用[无服务器](https://serverless.com)来简化部署和基础设施，没有 Sinatra、Rack 或任何样板文件。

目标是证明您可以构建一个 API，并且几乎忘记您实际上是在处理 web 请求，只需关注 GraphQL 模式和业务逻辑。它看起来怎么样？有三个关键元素: ***serverless.yml*** 通过 HTTP POST 公开我们的函数:

```
service: serverless-ruby-graphql

provider:
  name: aws
  runtime: ruby2.5
  region: eu-west-1
  stage: dev

functions:
  api:
    handler: app.request
    events:
      - http:
          path: api
          method: post

plugins:
  - serverless-hooks-plugin

custom:
  hooks:
    package:initialize:
      - bundle install --deployment
    deploy:finalize:
      - rm -fr .bundle
      - rm -fr vendor
      - bundle install 
```

Enter fullscreen mode Exit fullscreen mode

***app.rb*** 处理传入的请求，并将所有工作委托给 GraphQL Ruby:

```
require 'json'
require_relative "app/graphql/schema"

def request(event:, context:)
  puts "Received Request: #{event}"

  body = Schema.execute(event["body"]).to_json

  {
    statusCode: 200,
    body: body
  }
rescue StandardError => e
  puts e.message
  puts e.backtrace.inspect

  {
    statusCode: 400,
    body: JSON.generate("Bad request, please POST a request body!")
  }
end 
```

Enter fullscreen mode Exit fullscreen mode

最后， ***app/*** 一个包含所有 GraphQL 模式和模型的文件夹:

[https://github . com/oriolgual/server less-ruby-graph QL/tree/master/app](https://github.com/oriolgual/serverless-ruby-graphql/tree/master/app)

虽然这样做没问题，但请记住，这不是生产就绪代码。

## 让我们用 Lambda 构建一切

[![](img/8c713ca583e4851be06ff8c2f2d378b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZQIavaLx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q5p5yd19rvspce1wyxnf.jpg)

嗯，也许不是。用 AWS Lambda 和 Ruby 构建东西很简单，但是我会用它来构建我的下一个 GraphQL API 吗？大概不会。

我并不完全赞同将 AWS Lambda 与面向用户的 API 结合使用；响应时间太不可预测，而且相当高(是的，即使你让你的 Lambdas [保持温暖](https://serverless.com/blog/keep-your-lambdas-warm/))。如果您的 lambda 需要访问 vpc 内部的资源(例如，如果您想要访问数据库，这是必需的)，事情会变得[甚至]([https://www . robertvojta . com/AWS-journey-API-gateway-Lambda-VPC-performance/](https://www.robertvojta.com/aws-journey-api-gateway-lambda-vpc-performance/))[更慢](https://www.reddit.com/r/aws/comments/6lfubn/aws_lambda_vpc_redis_slow/)。

我真的很喜欢 Lambda，但我认为它在用于响应事件(如 S3 上传或 Kinesis 流)时会大放异彩，在这种情况下，你不会真的关心一些额外的延迟，但可伸缩性可能是一个问题。

我渴望尝试与 [Knative](https://github.com/knative/docs/) 、 [Kubeless](https://kubeless.io) 或 [OpenWhisk](https://openwhisk.apache.org) 类似的东西。到目前为止，我只使用了 AWS Lambda，我想将它与其他解决方案进行比较，并更好地了解它是否是部署无服务器、面向用户的 API 的好选择。