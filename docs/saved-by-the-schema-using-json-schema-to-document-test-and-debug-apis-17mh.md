# 由模式保存:使用 JSON 模式记录、测试和调试 API

> 原文：<https://dev.to/heroku/saved-by-the-schema-using-json-schema-to-document-test-and-debug-apis-17mh>

Heroku 有许多公共 API 端点。这些端点中的每一个都需要测试，以便我们知道它们是如何工作的，并且记录下来，以便我们的客户(和其他 API 消费者)知道它们是如何工作的。继续关注，我们将了解 Heroku 如何使用 [JSON 模式](https://json-schema.org/)来测试和记录我们的[平台 API](https://devcenter.heroku.com/categories/platform-api) ，以及它如何帮助我们发现一个意想不到的错误，根源在于 [Oj gem](https://github.com/ohler55/oj) 解析大小数的方式。

JSON 模式文件就像定义其他 JSON 文档的结构和语义的蓝图。将 JSON 模式文件应用于 JSON 文档时，您可以确定文档是有效的(符合模式)还是无效的(不符合模式)。

那么我们 Heroku 如何使用 JSON Schema 来记录和测试我们的公共 API 呢？

## 用 JSON 模式记录 API

维护公共 API 最重要的部分之一是确保文档总是最新的。

Heroku 的工程团队确保我们的公共 API 文档正确的一种方法是由我们的 CI 服务调用 rake 任务。rake 任务检查当前分支中对 JSON 模式文件的任何更改。如果有变化，它使用 [prmd](https://github.com/interagent/prmd) 将这些更新的 JSON 模式文件转换成 markdown 文件。当变更被部署时，那个 markdown 文件被发布为我们的公共[平台 API 引用](https://devcenter.heroku.com/articles/platform-api-reference)。

## 用 JSON 模式测试 API

通过 JSON Schema 记录 API 端点比手动更新文档要好，但是这个过程不能确保文档的准确性。为了验证 JSON 模式文件生成的文档的准确性，必须根据 JSON 模式文件验证 API 请求和响应。

在平台 API 的测试套件中，我们使用[委员会的 gem](https://github.com/interagent/committee) 来查看来自我们 API 的 JSON 响应是否匹配相应的 JSON 模式文件的结构。如果一个端点的测试通过了 gem 委员会提供的`assert_schema_conform`方法，我们知道我们的 API 请求和响应匹配 JSON 模式中的定义。如果测试失败了`assert_schema_conform`，我们知道要么我们需要改变我们的 JSON 模式定义，要么我们需要更新 API 端点本身来匹配相应的 JSON 模式文件。

当我们在 Heroku 开始用 committee 测试我们的 API 时，它立即发现了现有 JSON 模式文件和各种 API 端点实际返回的内容之间的一些不一致——事实证明这是一份不断赠送的礼物。

## 使用 JSON 模式查找并修复 API 边缘案例

一般来说，编写测试的一个挑战是编写充分覆盖所有现实生活中的边缘案例的测试。编写比较 API 响应和 JSON 模式文件的测试也不例外。

最近，我在为我们的 Heroku Enterprise 用户开发一个功能，允许他们检索他们的帐户或团队的每月或每天的使用数据。我们正在对新端点进行一些初始的`curl`请求，这时我们注意到一些奇怪的事情:当`dyno`使用值是一个十进制数时，它是一个字符串。当`dyno`的使用值是一个整数时，它就是一个数字。

在我们的 API 文档中，我看到`dynos`值被定义为一个数字。因为我们的 API 文档是从 JSON 模式生成的，所以我知道这些文档可能反映了我们的 JSON 模式文件。

我回到了端点的 JSON 模式文件，其中包括以下定义:

```
"dynos":  {  "description":  "dynos used",  "example":  1.548,  "readonly":  true,  "type":  [  "number"  ]  } 
```

因此，根据 JSON 模式定义，我们应该有一个数字。为了测试我在`curl`请求中看到的内容，我为一个团队编写了一个新的测试，其中 dyno 的用法是一个有许多小数位的数字。当我使用 committee gem 的`assert_schema_conform`方法运行新测试时，我得到了以下错误:

```
Failure/Error: assert_schema_conform

     Committee::InvalidResponse:
       Invalid response.

       #/0/dynos: failed schema #/definitions/team-usage/properties/dynos: For 'properties/dynos', "1967.4409999999837" is not a number. 
```

好消息！我复制了我在生产中看到的情况:有时，`dynos`值作为字符串(引号中的数字)返回，而不是我们预期的数字。为了确保这一点，我运行了旧的测试(对于 dyno 使用是整数的团队)，并且通过了`assert_schema_conform`检查。所以在这种情况下，`dynos`值是一个与模式预期相似的数字。又是好消息！我再现了`dynos`值是一个数字的情况。现在要找出原因....

因为两个响应都通过同一个序列化器，唯一可能的答案是在 JSON 解析这些值时发生了一些事情。在我们的端点中，我们序列化响应，然后调用`MultiJson.dump(object)`。 [MultiJson](https://github.com/intridea/multi_json) 只是“一个用于 Json 处理的通用可交换后端”,所以为了进一步调试，我需要查看我们使用的是哪个解析适配器。在我们的例子中，是 [Oj](https://github.com/ohler55/oj) 。

在一个`irb`会话中，我运行了以下内容:

```
require 'multi_json'
=> true
 MultiJson.use :oj
=> MultiJson::Adapters::Oj
MultiJson.load('0').class
=> Integer
MultiJson.load('1967.44').class
=> Float
MultiJson.load('1967.4409999999837').class
=> BigDecimal 
```

这一切看起来都在意料之中。但是当进一步挖掘时，我的问题变得更加明显:

```
MultiJson.dump(MultiJson.load('0'))
=> "0"
irb(main):002:0> MultiJson.dump(MultiJson.load('1967.44'))
=> "1967.44"
MultiJson.dump(MultiJson.load('1967.4409999999837'))
=> "\"1967.4409999999837\"" 
```

答对了。MultiJson 解析有许多小数位的数字(一个 BigDecimal)不同于其他数字(注意返回值周围的额外引号)。

在浏览了 Oj 回购之后，我发现了来自维护者的以下[评论](https://github.com/ohler55/oj/issues/376#issuecomment-299470766):

> json gem 和 Rails 使用字符串格式表示 BigDecimal。Oj 现在尽力模仿两者，所以它现在也返回一个字符串。

虽然我们的 API 不能在 Rails 上运行，但我很想知道为什么 Rails 会将 BigDecimals 解析为字符串。从[开始的文档](https://api.rubyonrails.org/v3.1/classes/BigDecimal.html#method-i-as_json):

> 一个 [BigDecimal](https://api.rubyonrails.org/v3.1/classes/BigDecimal.html) 自然会被表示为一个 JSON 数。然而，大多数库将非整数 JSON 数字直接解析为浮点数。使用这些库的客户机通常会得到一个错误的数字，除了用 JSON 代码本身手工检查字符串之外，没有其他方法可以恢复。

这就是返回 JSON 字符串的原因。JSON 文字不是数字，但是如果另一端通过约定知道数据应该是 BigDecimal，它仍然有机会对字符串进行后处理并获得实际值。

简而言之，Oj 将 BigDecimals 解析为字符串，因为 Rails 将 BigDecimals 解析为字符串。Rails 将 BigDecimals 解析为字符串，以实现更高的特异性。

如果我们的 API 端点想要拥有尽可能高的特异性，我们可能会保留这个默认行为，并且可能将这个字段的所有值都转换为字符串。虽然可以将 JSON 模式中的字段定义为字符串或数字，但是从 API 设计的角度来看，这样做是不可取的。这是因为[对于给定值](https://github.com/interagent/http-api-design/blob/master/en/responses/provide-standard-response-types.md)总是返回相同的数据类型是好的 API 设计(即，API 消费者可能不总是知道*他们将接收哪个*数字，但是他们知道它将总是一个数字，而不是字符串、数组、散列等。).

[Ruby 中的浮点数精度限制在 15 位小数](https://medium.com/@irio/ruby-numeric-types-dos-and-do-not-s-5836250dbc58),对于这个 API 端点中的`dyno`值，15 位小数已经足够了，所以转换为浮点数也没问题。为了防止 Oj 将 BigDecimals 转换为字符串，我们将应用程序的 MultiJson 配置更新为:

```
MultiJson.use :oj
MultiJson.load_options = {
  bigdecimal_load: :float,
} 
```

当我为一个具有 BigDecimal `dyno`值的团队重新运行`assert_schema_conform`测试时，它通过了，所以我知道我们的模式，因此我们的文档，反映了从我们的 API 返回的真实值。

Heroku 对 JSON Schema、prmd 和 committee gem 的使用使得测试、验证和记录 API 现在将大小数解析为浮点数而不是字符串变得很容易。