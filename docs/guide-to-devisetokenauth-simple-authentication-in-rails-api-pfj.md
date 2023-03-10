# devise _ token _ auth 指南:Rails API 中的简单认证

> 原文：<https://dev.to/risafj/guide-to-devisetokenauth-simple-authentication-in-rails-api-pfj>

我想为我的 Rails API 创建一个认证系统，但是关于 API(没有客户端)的一个问题是，您不能使用会话或 cookies 进行认证。
所以取而代之，我用了宝石 [`devise_token_auth`](https://github.com/lynndylanhurley/devise_token_auth) ，它用的是代币。简单地说，它是这样工作的:当您发出注册或登录的 HTTP 请求时，响应头为您提供身份验证令牌，您在后续的 HTTP 请求头中发送这些令牌来证明您已经过身份验证。

虽然[官方文档](https://devise-token-auth.gitbook.io/devise-token-auth/)提供了你需要的大部分信息，但是有几点我觉得很困惑，所以我把这篇文章留给以后参考。希望有帮助！

注意:本指南适用于 Linux 或 MacOS。

# 实现步骤

也请随意查看我创建的基本库[作为如何使用这个宝石的概念证明。](https://github.com/risafj/demo-for-devise-token-auth)

## 1。安装设备令牌认证

将以下内容添加到您的`Gemfile`中，然后从命令行运行`bundle`:

```
gem 'devise_token_auth' 
```

## 2。生成必要的文件

从命令行执行这个命令。

```
rails g devise_token_auth:install User auth 
```

这将做许多事情，包括:

*   创建一个`User`模型，它存储用户的电子邮件地址等信息，以及相应的迁移文件
*   在您的`config/routes.rb`文件中添加一行，指定身份验证端点的路由，如注册或登录(如果您想将它路由到除`/auth/`之外的某个地方，用其他东西替换命令中的“`auth`”

关于这个命令的详细列表，请查看[文档](https://devise-token-auth.gitbook.io/devise-token-auth/config)。

### 更新

根据 Rafael 下面的评论，现在需要将`extend Devise::Models`添加到这里生成的`User`模型文件中。细节可以在这一期 Github [中找到](https://github.com/lynndylanhurley/devise_token_auth/issues/1276#issuecomment-478866479)。

[![rafaso profile image](img/944f09072e3fcd81de8cdcb8d915c27b.png) ](/rafaso) [ Rafael Oliveira ](/rafaso) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/rafa_so) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/rafa-so) [<time datetime="2019-10-25T21:54:29Z">Oct 25 '19</time>](/rafaso/comment/h2ig)

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN" "[http://www.w3.org/TR/REC-html40/loose.dtd">](http://www.w3.org/TR/REC-html40/loose.dtd%22%3E)

嗨！很好的帖子，在安装完 devise _ token _ auth 之后，只添加了下面的语句:“扩展 device::Models”来开始用户的模型。这是 rails 版本 6。

别的也可以:)

## 3。迁移您的数据库

在命令行中运行`rails db:migrate`来应用在步骤#2 中创建的迁移文件，它可能看起来类似于`db/migrate/YYYYMMDDTTTT_devise_token_auth_create_users.rb`。

## 4。配置你的初始化文件

转到您的`config/initializers/devise_token_auth.rb`文件(也是在步骤#2 的`rails g`命令中创建的)。

同样，[文档](https://devise-token-auth.gitbook.io/devise-token-auth/config/initialization)中有您可以进行的配置的完整列表，但仅举一个例子:

```
config.change_headers_on_each_request = false 
```

默认情况下，授权头会随着每个请求而变化。这意味着您将在每次请求时获得新的令牌，并且每次都必须发送回不同的令牌。我希望能够继续使用相同的令牌，所以我通过将它设置为`false`来关闭它。
重用您的令牌并不是最佳的安全实践，因此在生产中每次使用新的令牌可能会更好。

## 5。对 JSON 请求禁用伪造保护

Rails 控制人员预先设定了措施来防范跨站点请求伪造(CSRF)攻击。这涉及到将呈现的 HTML 中的令牌与 Rails 自动存储在会话中的令牌进行比较，但是对于 API，我们没有会话，将使用我们自己的令牌，所以这是不必要的(这里的[解释了](https://api.rubyonrails.org/classes/ActionController/RequestForgeryProtection.html))。

> 重要的是要记住，XML 或 JSON 请求也会受到影响，如果您正在构建一个 API，您应该在 ApplicationController 中更改伪造保护方法(默认情况下::exception)

因此，请在`app/controllers/application_controller.rb`中禁用 JSON 格式请求的伪造检查。
注意:只有当你的所有请求都通过你的 API 时才这样做。

```
class ApplicationController < ActionController::Base
  protect_from_forgery unless: -> { request.format.json? }
end 
```

### 注意事项

*   跳过这一步会导致以下错误:`ActionController::InvalidAuthenticityToken in DeviseTokenAuth::RegistrationsController#create`。
*   如果您的`ApplicationController`继承自`ActionController::API`(如果您用`--api`标志初始化项目，应该是这种情况)，这一步应该是不必要的，因为默认情况下没有伪造保护。

## 6。尝试注册一个用户

现在，让我们试着注册一个测试用户。使用`rails s`从命令行启动 Rails 服务器，并使用以下参数向`localhost:3000/auth/`(或您的自定义路由)发送 HTTP POST 请求。

```
{
  "email": "test@email.com",
  "password": "password",
  "password_confirmation": "password"
} 
```

它应该返回状态 200 -成功。现在你的认证系统工作了！

## 7。向您的控制器添加身份验证

接下来，在相应的控制器文件中添加一行，使身份验证成为必要。例如，假设我们正在为一个存储书籍信息的数据库构建一个 API。我们有文件`books_controller.rb`用于在数据库中添加或删除书籍。我们希望在添加或删除条目之前对用户*进行身份验证。* 

```
class BooksController < ApiController
  before_action :authenticate_user!

  # Code for methods such as create and delete should come here.
end 
```

现在，每当您向 books 控制器中的任何方法发送 HTTP 请求时，都会返回一个错误，除非您通过了身份验证。

# 实际使用情况

我前面提到过，注册用户的 HTTP 请求是对`localhost:3000/auth/`的 POST 请求。关于方法的完整列表(例如，登录/退出用户，并更改他们的密码)，请参考[文档](https://devise-token-auth.gitbook.io/devise-token-auth/usage)。您可以看到需要什么请求类型、路由和参数。

最后说一下实际使用情况。以下是涉及的步骤:

1.  发送身份验证请求(注册或登录)
2.  返回状态 200，标头中包含有效的身份验证令牌
3.  在您的下一个请求中，在您的邮件头中发送这些令牌

所需的令牌类别包括:

*   访问令牌
*   客户
*   用户界面设计（User Interface Design 的缩写）

就这么简单！

我还想讨论一下测试技巧，但是因为这太长了，我会在另一篇文章中写。感谢阅读:)

### 更新

这里是关于测试的文章！

[![risafj image](img/d4406b9edbd3b0a4c23589b890404d51.png)](/risafj) [## 测试技巧:使用 Rails API 中的 devise _ token _ auth 进行身份验证

### 村上里沙藤井 3 月 24 日 193 分钟阅读

#ruby #rails #testing #authentication](/risafj/tips-for-testing-authentication-with-devisetokenauth-in-rails-api-55pj)

### 附加更新

看起来像是`trackable`模块从`devise`宝石中被丢弃了，而`devise_token_auth`正是基于此。所以`User`模型中的默认配置，包括`:trackable`，很可能会产生这样的错误:`NoMethodError: undefined method 'current_sign_in_at'`。
有关如何解决此问题的详细信息，请参见以下链接:

*   [https://stack overflow . com/questions/55735895/nomethoderror-undefined-method-current-sign-in-at-for-user 0x 000055 ce 01 DCF 0a](https://stackoverflow.com/questions/55735895/nomethoderror-undefined-method-current-sign-in-at-for-user0x000055ce01dcf0a)
*   [https://www . bounty source . com/issues/75245078-cant-log-in-nomethoderror-undefined-method-current _ sign _ in _ at-for-user-0x 000055 e 053 c 79 c 58](https://www.bountysource.com/issues/75245078-cant-log-in-nomethoderror-undefined-method-current_sign_in_at-for-user-0x000055e053c79c58)