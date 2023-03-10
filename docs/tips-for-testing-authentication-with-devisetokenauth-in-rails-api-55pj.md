# 测试技巧:使用 Rails API 中的 devise _ token _ auth 进行身份验证

> 原文：<https://dev.to/risafj/tips-for-testing-authentication-with-devisetokenauth-in-rails-api-55pj>

之前，我写过一篇关于如何用`devise_token_auth` gem 为 Rails API 创建认证系统的博文。

[![risafj](img/d4406b9edbd3b0a4c23589b890404d51.png)](/risafj) [## devise _ token _ auth 指南:Rails API 中的简单认证

### 村上里沙藤井二月九日 194 分钟阅读

#rails #ruby #devisetokenauth #authentication](/risafj/guide-to-devisetokenauth-simple-authentication-in-rails-api-pfj)

但是说实话，对我来说最不直观的部分不是添加认证系统本身，而是测试它和任何使用它的功能。所以这篇文章关注的是测试，更具体地说:

1.  如何为认证系统编写测试？
2.  如何为需要认证的功能编写控制器测试？

第一点不言自明。至于第二点:假设您正在为图书馆的图书创建一个应用程序。您可以通过对`/books`的 HTTP POST 请求将新书添加到数据库中，但是您需要被认证才能这样做。如果不涉及身份验证，您可以像下面这样测试添加书籍的方法，但是在我们的例子中这个测试会失败，因为请求没有经过身份验证。

```
# test/controllers/books_controller_test.rb
test 'should create new book' do
  post 'books/',
    params: {
    name: 'Harry Potter'
    }
  assert_response :success
end 
```

Enter fullscreen mode Exit fullscreen mode

注意:该指南适用于 Linux 或 MacOS 系统，并使用 minitest 进行测试。

# 概述

以下是相关的步骤。

*   编写我们将在`test/helpers/authorization_helper.rb`的测试中使用的助手授权方法
*   创建`test/authorization_test.rb`来测试我们的授权助手代码
*   在需要认证的方法测试中，调用`authorization_helper.rb`中的授权方法

# 第一步。创建授权助手文件

我上面描述的问题(未认证请求测试失败)的一个解决方案是在每个需要认证的方法测试的第一部分增加一个登录步骤。但是这将导致大量冗余代码。处理这个问题的一个更好的方法是用必要的认证方法创建一个帮助文件，并在控制器测试中调用这些方法。

在`test/helpers`中，创建一个名为`authorization_helper.rb`的文件。在内部，编写您将在测试中使用的方法，比如登录和从消息头获取认证令牌(如下所示)。

```
module AuthorizationHelper
  def sign_up(user)
    # The argument 'user' should be a hash that includes the params 'email' and 'password'.
    post '/auth/',
      params: { email: user[:email],
                password: user[:password],
                password_confirmation: user[:password] },
      as: :json
  end

  def auth_tokens_for_user(user)
    # The argument 'user' should be a hash that includes the params 'email' and 'password'.
    post '/auth/sign_in/',
      params: { email: user[:email], password: user[:password] },
      as: :json
    # The three categories below are the ones you need as authentication headers.
    response.headers.slice('client', 'access-token', 'uid')
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

请记住，这段代码不能按原样运行，因为这意味着要在内部测试中调用。

# 第二步。测试您的授权助手代码

在您的`test`文件夹中，创建一个名为`authorization_test.rb`的文件。然后，您可以测试您的助手文件中的方法。

例如:

```
test 'sign up and log in user one' do
    user_one = { email: 'userone@test.com', password: 'password' }
    sign_up(user_one)
    assert_response :success

    (user_one)
    assert_response :success
  end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经确认了身份验证是有效的，如果您的控制器测试失败了，您可以假设这不是因为身份验证问题。

# 第三步。将您的助手方法添加到需要身份验证的测试中

回到我们的 books 应用程序示例——添加新书的控制器测试需要在 HTTP 请求中包含身份验证令牌。所以采取以下步骤。

## 1)要求并包含您的助手文件

在使用身份验证的控制器测试中包含助手文件。

```
# Please use your actual relative path to this file.
require_relative '../helpers/authorization_helper' 
```

Enter fullscreen mode Exit fullscreen mode

同样，像这样包括这个模块。

```
class BooksControllerTest < ActionDispatch::IntegrationTest
include AuthorizationHelper
end 
```

Enter fullscreen mode Exit fullscreen mode

## 2)在设置方法中进行验证

在每次测试之前都会调用`setup`方法，因此将您的身份验证方法放在这里以避免冗余代码。

```
def setup
  test_user = { email: 'user@test.com', password: 'testuser' }
  sign_up(test_user)
  @auth_tokens = auth_tokens_for_user(test_user)
  end 
```

Enter fullscreen mode Exit fullscreen mode

## 3)在报头中包含授权令牌

然后，只需在测试头中包含 auth 令牌。就像下面加一行一样简单。

```
test 'should create new book' do
  post 'books/',
    params: {
    name: 'Harry Potter'
    },
    headers: @auth_tokens

    assert_response :success
end 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们已经讨论了如何为授权代码编写测试，以及如何在其他测试中发送经过身份验证的 HTTP 请求。感谢您的阅读，如果有任何需要澄清的地方，请告诉我。