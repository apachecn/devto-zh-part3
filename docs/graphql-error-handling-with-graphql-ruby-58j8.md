# 用 graphql-ruby 处理 GraphQL 错误

> 原文：<https://dev.to/masakazutakewaka/graphql-error-handling-with-graphql-ruby-58j8>

如果你想开发一个稳定的、高可用性的应用程序，错误处理是你必须非常注意的事情。与 RESTful 相比，GraphQL 是一个相当新的协议，考虑到它与客户端通信的独特方式，应该用完全不同的方法来处理它。
当我开始用 Ruby on Rails 和 [graphql-ruby](https://github.com/rmosolgo/graphql-ruby/) 开发一些应用程序时，我意识到你还没有看到很多关于这个主题的文章，所以我决定分享我用 graphql 和 Ruby on Rails 处理错误的方法。

首先，我将快速解释 GraphQL 的规范和实践，然后进入主题，即我如何用 graphql-ruby 实现错误处理。

# 目录

*   GraphQL 的规范和实践
*   GraphQL 中出现的各种错误
*   用 graphql-ruby 实现
*   具有营救方法的安全网
*   用 add_error 方法处理多个错误

# GraphQL 的规范和实践

## 练习 GraphQL 中的响应格式

GraphQL 的一个惯例是每个响应都返回 http status 200，发生的错误包含在响应体的`errors`键中。
这是因为在 GraphQL 中，您可以在一个请求中包含多个查询。

> 因为 GraphQL 允许在同一个请求中发送多个操作，所以请求很可能只是部分失败，并返回实际数据和错误。

[https://www . graph . cool/docs/FAQ/API-eep 0 ugh 1 wa/# how-do-error-handling-work-with-graph cool](https://www.graph.cool/docs/faq/api-eep0ugh1wa/#how-does-error-handling-work-with-graphcool)

这是一种实践，而不是规范，但相关工具如 Apollo 和 graphql-ruby 遵循这种实践，这也让我们遵循它。

## GraphQL 关于响应格式的规范

那你怎么用 GraphQL 表示错误呢？让我们在这里看看 GraphQL 的规范。

[https://facebook.github.io/graphql/June2018/#sec-Errors](https://facebook.github.io/graphql/June2018/#sec-Errors)

根据 GraphQL 的规范，response 的格式是带有名为`data`和`errors`的关键字的 Hash。
`data`是有实际数据的键，`errors`是执行过程中出现错误的键。

```
{  "errors":  [  {  "message":  "hogehoge",  "extensions":  {  "bar":  "bar"  }  }  ],  "data":  {  "user":  {  "name":  "Jon"  }  }  } 
```

顺便说一下，`errors`中散列的格式在规范中有规定，因为它有 3 个键，分别叫做`message`、`location`和`path`。如果你想让它有另一个密钥，创建一个名为`extensions`的密钥，然后把你的密钥放进去，如下所示。

```
{  "errors":  [  {  "message":  "Name for character with ID 1002 could not be fetched.",  "locations":  [  {  "line":  6,  "column":  7  }  ],  "path":  [  "hero",  "heroFriends",  1,  "name"  ],  "extensions":  {  "code":  "CAN_NOT_FETCH_BY_ID",  "timestamp":  "Fri Feb 9 14:33:09 UTC 2018"  }  }  ]  } 
```

您希望以这种方式添加您自己的密钥，因为如果您在与其他默认密钥(如`message`和`line`)相同的级别创建您的密钥，它可能会与将来添加的密钥发生冲突。

> GraphQL 服务不应该向错误格式提供任何附加条目，因为它们可能与本规范未来版本中可能添加的附加条目相冲突。

[https://facebook.github.io/graphql/June2018/#sec-Errors](https://facebook.github.io/graphql/June2018/#sec-Errors)

# 使用 GraphQL 得到的各种错误

好了，现在我们知道了如何在 GraphQL 中表达错误。
接下来，让我们看看使用 GraphQL 可以得到什么样的错误。
我指的是 Apollo 关于错误处理的文章。

使用 GraphQL + Apollo 进行全栈错误处理🚀–阿波罗 GraphQL

这是我们用来对错误进行分类的两个角度。

*   是客户端还是服务器出了问题？
*   错误发生在哪里？

我们可以将客户端错误分为三种类型。

*   解析错误
    *   查询语法错误
*   合法性错误
    *   类型检查阶段出错
*   执行错误
    *   认证错误

服务器端错误是发生在 Rails API 代码中的错误。

既然我们已经发现了什么样的错误，让我们看看如何在响应中表达它们。
我们希望统一规范中规定的所有响应的格式，以便像 Apollo 这样的客户端工具可以轻松解析它们。
此时，我们会将详细的错误信息放入`message`键，并将名为`code`的键放入代表状态代码的`extensions`键。
这实际上与规范中示例响应的格式相同。

如果在身份验证阶段出现错误，响应将如下所示。

```
"errors":  [  {  "message":  "permission denied",  "locations":  [],  "extensions":  {  "code":  "AUTHENTICATION_ERROR"  }  }  ] 
```

如果 Rails API 代码中出现错误，响应将如下所示。

```
"errors":  [  {  "message":  "undefined method 'hoge' for nil",  "locations":  [],  "extensions":  {  "code":  "INTERNAL_SERVER_ERROR"  }  }  ] 
```

# 用 graphql-ruby 实现

既然您已经知道如何在响应中表达错误，那么让我们看看如何用 graphql-ruby 实现这些错误。

## 如何用 graphql-ruby 捕捉并返回错误？

您可以通过像这样引发`GraphQL::ExecutionError`来将错误放入响应中的`errors`键。

```
def resolve(name:)
  user = User.new(name: name)
  if user.save
    { user: user }
  else
    raise GraphQL::ExecutionError, user.errors.full_messages.join(", ")
  end
end 
```

## 认证错误

让我们看一下这个例子，它展示了如何处理一个认证错误。

这个例子基于这样一个前提，即您可以通过调用`current_user`方法来获得您的登录会话。

```
class GraphqlController < ApplicationController
  def execute
    variables = ensure_hash(params[:variables])
    query = params[:query]
    operation_name = params[:operationName]
    context = { current_user: current_user }
    result = SampleSchema.execute(query, variables: variables, context: context, operation_name: operation_name)
    render json: result
    #...
  end
  #...
end 
```

然后你用错误码`AUTHENTICATION_ERROR`引发`GraphQL::ExecutionError`取决于`resolve`方法中`context[:current_user]`的值。

```
def resolve(name:, sex:)
  raise GraphQL::ExecutionError.new('permission denied', extensions: { code: 'AUTHENTICATION_ERROR' }) unless context[:current_user]

  #...
end 
```

使用这种实现，当发生身份验证错误时，您会得到这样的响应。

```
"errors":  [  {  "message":  "permission denied",  "locations":  [  {  "line":  3,  "column":  3  }  ],  "path":  [  "createUser"  ],  "extensions":  {  "code":  "AUTHENTICATION_ERROR"  }  }  ] 
```

# 安全网与救援 _ 脱离方法

现在您可能已经知道，统一响应的格式非常重要，以便客户端可以轻松地解析它们。
如果发生的错误没有被挽救，一般 500 内部服务器错误被返回给客户端，这迫使它们准备得到两种类型的响应。
很明显，你不希望这种情况发生，因为你希望客户能够自如地处理回复。
使用 graphql-ruby，您可以确保响应采用与`rescue_from`方法相同的格式。

```
class SampleSchema < GraphQL::Schema
  rescue_from(StandardError) do |message|
    GraphQL::ExecutionError.new(message, extensions: {code: 'INTERNAL_SERVER_ERROR'})
  end

#...
end 
```

响应将如下所示。

```
"errors":  [  {  "message":  "hogehoge",  "extensions":  {  "code":  "INTERNAL_SERVER_ERROR"  }  }  ] 
```

顺便说一下，我写了补丁将错误类对象传递给方法:)

[扩展 graph QL::Schema::RescueMiddleware # attempt _ rescue by masakazutakewaka Pull Request # 2140 rmosolgo/graph QL-ruby GitHub](https://github.com/rmosolgo/graphql-ruby/pull/2140)

它还没有包含在一个稳定的 blanch 中，所以从技术上来说，你现在只能传递一个 String 对象给这个方法...

请记住，这个`rescue_from`方法在 1.9 版本中不可用。
这是因为在版本 1.9 中不支持`GraphQL::Schema::RescueMiddleware`类，而版本 1.9 中定义了`rescue_from`方法。
T5】graph QL-解释器

在 1.9 版本中，你没有方法的替代品，我们甚至不知道替代它的额外特性目前看起来会是什么样子。
[GraphQL::Execution::解释器和 rescue_from 兼容性问题# 2139 rmosolgo/graph QL-ruby GitHub](https://github.com/rmosolgo/graphql-ruby/issues/2139)

# 用 add_error 方法处理多个错误

在某些情况下，您希望将响应中发生的所有错误捆绑在一起。
其中一种情况是当你有一个类似注册界面的多用户输入的突变。
使用 graphql-ruby，你可以用`add_error`方法捆绑多个错误。

```
module Mutations
  class CreateUser < GraphQL::Schema::RelayClassicMutation
    argument :name, String, required: true
    argument :sex, String, required: true

    field :user, Types::UserType, null: true

    def resolve(name:, sex:)
      user = User.new({ name: name, sex: sex })
      if user.save
        { user: user }
      else
        build_errors(user)
        return # rescue_from is invoked without this
      end
    end

    def build_errors(user)
      user.errors.map do |attr, message|
        message = user[attr] + ' ' + message
        context.add_error(GraphQL::ExecutionError.new(message, extensions: { code: 'USER_INPUT_ERROR', attribute: attr }))
      end
    end
  end
end 
```

包含多个错误的响应如下所示。

```
"errors":  [  {  "message":  "hoge already exists",  "extensions":  {  "code":  "USER_INPUT_ERROR",  "attribute":  "name"  }  },  {  "message":  "fuge already exists",  "extensions":  {  "code":  "USER_INPUT_ERROR",  "attribute":  "sex"  }  }  ] 
```

另外，还有一种方法可以实现这一点，那就是为错误定义 GraphQL 类型，并将错误放在`data`键中。
[https://github . com/rmosolgo/graph QL-ruby/blob/master/guides/mutations/mutation _ errors . MD # errors-as-data](https://github.com/rmosolgo/graphql-ruby/blob/master/guides/mutations/mutation_errors.md#errors-as-data)

# 结论

在本文中，我介绍了如何在 GraphQL 中进行错误处理，以及如何使用 graphql-ruby 实现它。GraphQL 的规范写得很好，也不会太长，所以我推荐你读一读。
希望这篇文章能对那些正在 GraphQL 中处理错误的人有所帮助。

最后，我正在加拿大找工作，所以任何消息或联系都是非常受欢迎的！

电子邮件:[takewakamma@gmail.com](mailto:takewakamma@gmail.com)

只要是加拿大，我都可以重新安置:)

感谢您的阅读！

# 参考文献

*   [图表 QL](https://facebook.github.io/graphql/June2018/#sec-Errors)
*   [API | Graphcool 文档](https://www.graph.cool/docs/faq/api-eep0ugh1wa/#how-does-error-handling-work-with-graphcool)
*   使用 GraphQL + Apollo 进行全栈错误处理🚀–阿波罗 GraphQL
*   [图形 QL-ruby/overview . MD at master = rmosolgo/图形 QL-ruby github](https://github.com/rmosolgo/graphql-ruby/blob/master/guides/errors/overview.md#errors-as-data)
*   [主 rmosolgo/graphql-ruby GitHub 上的 graph QL-ruby/execution _ errors . MD](https://github.com/rmosolgo/graphql-ruby/blob/master/guides/errors/execution_errors.md)
*   [主 rmosolgo/graphql-ruby GitHub 上的 graph QL-ruby/mutation _ errors . MD](https://github.com/rmosolgo/graphql-ruby/blob/master/guides/mutations/mutation_errors.md)