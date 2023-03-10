# 听说过 API Blueprint 吗？📘

> 原文：<https://dev.to/juanmanuelramallo/have-you-heard-of-api-blueprint-pp5>

最近，我遇到了为我正在构建的 API 提供某种文档的需求。那时，我从未听说过 API 蓝图规范。虽然它们可能是编写一个好的 API 文档的几种选择，但我发现这一个非常有帮助，原因如下。

# 1。易于阅读

路段被很好地定义并按路线分组。每个路由可以有几个不同的 HTTP 方法，每个方法都有相应的名称。文件显示:

1.  列出的属性及其描述(需要时)
2.  请求示例(标题和正文)
3.  这些示例的响应(也包括标题和正文)

[查看更多。](https://apiblueprint.org/documentation/tutorial.html)

# 2。模拟服务器

这种语言允许您创建一个模拟服务器，任何人都可以使用它来测试实际服务器的响应(有这方面的工具，[参见更多)。](https://apiblueprint.org/tools.html#mock%20servers))

因此，模拟服务器实际上是一个响应文档中定义的端点并返回预期响应的服务器(它不关心身份验证或头，因为它实际上不运行生产代码，它只是返回对端点命中的响应)。因此，在应用程序的早期阶段拥有这种服务器非常有帮助，因为客户端应用程序仍在构建中，后端尚未完全部署到基于生产/测试云的环境中。

# 3。很多工具

渲染器是这样的服务，当使用 API 时，它将你的蓝图作为输入，并创建一个漂亮的网页供他人参考。[养蜂场](https://apiary.io)就是其中之一，而且是应用广泛的一种。Apiary 可以与您的远程存储库连接，因此无论何时您的蓝图发生变化，它都会自动在文档页面中反映这些变化。

但是我不想写另一种语言，可能你也不想。这就是文档生成器库发生的地方。对于那些使用 Ruby❤️的人来说， [RSpec API 文档](https://github.com/zipmark/rspec_api_documentation)被证明是生成这个蓝图的一个很好的工具(尽管它也可以用来生成其他类型的文档)。您只需要编写一些 RSpec 测试并运行一个 rake 任务来生成蓝图。

这里有一个使用这个 gem 记录的基本端点的例子:

```
resource 'Books' do
  before do
    create_list :book, 5                # Creating some books 
  end

  route '/books', 'Books collection' do # Name of the section
    get 'Index' do                      # HTTP method with name
      example 'Ok' do                   # A request example
        do_request

        expect(status).to eq 200        # Basic expectation of the request
      end
    end
  end
end 
```

运行`rails docs:generate`之后，它会生成一个类似于
的文件

```
# Group Books

## Books collection [/books]

### Index [GET]
 + Request Ok
 + Response 200 () + Body

            {
              ... // The response body would be here
            } 
```

如果你正在写一个 API，试试这种语言。给你的开发伙伴一份适当的 API 文档，让他们高兴。

# 总而言之，有些事情我喜欢

1.  生产代码不会与生成文档的代码混淆
2.  所有与文档相关的代码都在一个地方
3.  一旦路线或控制器的响应发生变化，文件就会由生成器自动更新。我不需要写什么新东西。
4.  模拟服务器只使用来自文档的数据(它不运行生产代码)
5.  有很多服务来呈现文档(呈现器)，或者我可以[托管我自己的😎](https://github.com/jejacks0n/apitome)
6.  我的 API 蓝图文件位于同一个存储库中，所以我可以保证它总是与当前分支同步

 <sub><sub><sub><sub>[如果你想看全套生成文档的测试实例](https://github.com/juanmanuelramallo/target-mvd/tree/master/spec/acceptance)</sub></sub></sub></sub>