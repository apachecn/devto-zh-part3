# 用于 Rails 的 I18n:从 URL 参数和基本 I18n 用法中设置语言环境

> 原文：<https://dev.to/morinoko/setting-up-i18n-for-rails-with-locales-from-url-params-38pg>

为 Ruby/Rails 应用程序设置 I18n 相当简单，但是作为一个初学者，我经常遇到一些配置方面的问题(并试图解读 Rails 指南和其他人的博客中到底说了什么)。作为我自己和其他刚刚起步的开发者的参考，我将记录我在应用程序中使用 I18n 的步骤。

首先，您必须决定您的应用程序如何知道要使用哪种语言环境。您可以使用几种不同的策略，最常见的策略在 [Rails 指南](https://guides.rubyonrails.org/i18n.html)中有所概述:

1.  从域名设置区域设置
2.  从 URL 参数设置语言环境(在这篇文章中讨论)
3.  从用户首选项中设置区域设置
4.  选择隐含的区域设置，如 HTTP 语言头或 IP 地理位置

我个人喜欢使用 URL 参数来设置我的区域设置(上面的选项 2)，这意味着您可以在 URL 的末尾显示区域设置代码，如下所示:

```
# For the English version of the site:
https://www.myapp.com/en
https://www.myapp.com/en/login

# For the Japanese version:
https://www.myapp.com/ja
https://www.myapp.com/ja/login 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这种策略，因为它不需要太多的配置，而且它使语言对用户来说很明显，因为他们可以在 URL 中看到它。你可以使用上面的策略 1，通过设置域名或子域名的区域设置来做类似的事情(这样 URL 看起来就像`https://www.myapp.ja`或`https://www.ja.myapp.com`)，但是你需要多个域名，对我来说，我不喜欢改变域名的主要部分(例如`.com`部分)，也不喜欢把东西粘在域名的开头。

## 设置您的应用程序从 URL 参数中获取区域设置

为了让应用程序知道使用哪种语言环境，您需要确保在每个新页面请求的一开始就设置好语言环境。否则，Rails 将使用默认的语言环境(:en，除非另外配置)。一种方法是将下面的代码添加到`application_controller.rb`文件中:

```
class ApplicationController < ActionController::Base
  before_action :set_locale

  def set_locale
    I18n.locale = params[:locale] || I18n.default_locale
  end

  def default_url_options
    { locale: I18n.locale }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这是怎么回事？首先，定义了一个名为`set_locale`的方法，它将把`I18n.locale`变量设置为参数中的`:locale`变量。在这种情况下，`params[:locale]`将直接来自 url:例如，`https://www.myapp.com/en`中的`en`或`https://www.myapp.com/ja/login`中的`ja`。

如果由于某种原因没有`params[:locale]`变量，Rails 将使用上面代码的`|| I18n.default_locale`部分指定的默认语言环境。

第`before_action :set_locale`行确保在每个控制器请求开始时执行`set_locale`方法(也就是说，每当你进入应用程序中的任何页面/视图时)。

在为像`users_path`这样的路由使用帮助器时，`default_url_options`方法确保 url 中包含区域设置。否则，您每次都必须像这样包含区域设置参数，例如:`users_url(locale: I18n.locale)`。

顺便说一下，`default_locale`的默认语言将是英语，除非您另外指定。要更改默认值，您可以在您的`Application`类:
内的`config/application.rb`文件中设置它

```
module MyApp
  class Application < Rails::Application
    # Initialize configuration defaults for originally generated Rails version.
    config.load_defaults 5.2

    # Set the default locale to Japanese
    config.i18n.default_locale = :ja

    # more code...
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要确保 URL 路由正确，并且包含一个名为`:locale`的 URL 参数。一种方法是在`routes.rb`文件中使用一个作用域。

```
# config/routes.rb

Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html

  scope "/:locale" do
    resources :users, only: [:new, :show]
    root 'welcome#index'

    #... more routes
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

上述范围的问题是，如果有人遗漏了 URL 的地区部分，例如，`https://www.myapp.com`或`https://www.myapp.com/login`，他们将得到一个路由错误。要使`:locale`参数可选，您可以像下面的代码一样传入一个包含可用路线的正则表达式，因此如果 URL 的区域设置部分被忽略，应用程序将使用默认的区域设置:

```
# config/routes.rb

Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html

  scope "(:locale)", locale: /en|ja/ do
    resources :users, only: [:new, :show]
    root 'welcome#index'

    #... more routes
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在设置应该准备好了，你只需要确保在保存在`config/locales/`文件夹中的特殊`yaml`文件中提供每个地区的翻译。

## 区域设置文件，显示翻译，和一些提示

翻译文件(每个语言环境的 yaml 文件)包含映射为键/值对的应用程序的所有文本。每个文件包含相同的关键字，但每种语言的关键字值不同。例如，这里有一些可能用于英语和日语的短语:

```
# config/locales/en.yml

en:
  signup: "Sign  up"
  login: "Login"
  logout: "Logout"
  login_status: "Logged  in  as  %{username}." 
```

Enter fullscreen mode Exit fullscreen mode

```
# config/locales/ja.yml

ja:
  signup: "登録する"
  login: "ログイン"
  logout: "ログアウト"
  login_status: "%{username}としてログインしています。" 
```

Enter fullscreen mode Exit fullscreen mode

现在可以在视图中通过将键传递给`t`方法来访问这些翻译，该方法是`translate`的别名。所以如果你使用`t('signup')`，它会为英文页面打印出`Sign up`，为日文页面打印出`登録する`。

下面是一个使用翻译创建登录链接的例子:

```
<%= link_to t('login'), login_path %> 
```

Enter fullscreen mode Exit fullscreen mode

上面的`login_status`键演示了如何将变量传递给翻译字符串。只需将变量放在`%{}`和`username`之间，并像这样传递它:`t('login_status', username: current_user.username)`。下面是一个来自视图文件的例子:

```
<% if logged_in? %>
  <div id="login_status">
    <%= t('login_status', username: current_user.username) %>
  </div>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

为了组织你的翻译，键也可以被限定范围。例如，让我们将上面的翻译组织在一个`sessions`范围下:

```
# config/locales/en.yml

en:
  sessions:
    signup: "Sign  up"
    login: "Login"
    logout: "Logout"
    login_status: "Logged  in  as  %{username}." 
```

Enter fullscreen mode Exit fullscreen mode

现在可以用`.`符号来查找它们:

```
t('sessions.login') 
```

Enter fullscreen mode Exit fullscreen mode

或者另一种方式是引用范围:

```
t('login', scope: :sessions) 
```

Enter fullscreen mode Exit fullscreen mode

### 用于表单中 ActiveRecord 对象的翻译。

I18n 还为您提供了一种转换 ActiveRecord 模型属性的便捷方式。最近做表格的时候发现这个很有用。假设您需要一个注册表单来在 ActiveRecord 中创建新用户。使用 Rails 的`form_for`，它看起来像这样:

```
<div id="form-signup">
  <h1><%= t 'signup' %></h1>

  <%= form_for @user do |f| %>
    <div class="form-group">
      <%= f.label :username %>
      <%= f.text_field :username %>
    </div>

    <div class="form-group">
      <%= f.label :email %>
      <%= f.email_field :email %>
    </div>

    <div class="form-group">
      <%= f.label :password %>
      <%= f.password_field :password %>
    </div>

    <div class="form-group">
      <%= f.label :password_confirmation %>
      <%= f.password_field :password_confirmation %>
    </div>

    <%= f.submit t('signup') %>
  <% end %>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

如你所知，`label`助手会根据用户的属性自动创建标签:`:username`、`:email`、`:password`等。您还可以通过正确设置翻译文件，让它自动翻译属性名称。

为此，只需在翻译文件中添加一个`activerecord`范围键，该范围键嵌套了一个与您需要翻译的模型同名的范围键(在本例中为`user`)，该范围键还嵌套了相应属性的命名键。这些属性键的名称应该与 ActiveRecord 数据库属性的名称相同。

```
# config/locales/en.yml

en:
  activerecord:
    models:
      user: "User"
    attributes:
      user:
        username: "Username"
        email: "Email"
        password: "Password"
        password_confirmation: "Confirm  Password" 
```

Enter fullscreen mode Exit fullscreen mode

```
# config/locales/ja.yml

ja:
  activerecord:
    models:
      user: "ユーザー"
    attributes:
      user:
        username: "ユーザー名"
        email: "メールアドレス"
        password: "パスワード"
        password_confirmation: "パスワード確認" 
```

Enter fullscreen mode Exit fullscreen mode

I18n 现在知道每个表单标签使用什么文本了！

### 视图的惰性查找

惰性查找允许你为你的控制器视图映射出翻译，这样你就不用像`t('welcome.index.greeting')`那样输入长的钥匙链来引用键。

要为特定的控制器及其视图设置“字典”，请添加一个与控制器同名的键，然后添加嵌套在与视图名称对应的键之下的键。在下面的例子中，有用于`welcome`和`users`控制器的字典:

```
# config/locales/en

en:
  welcome:
    index:
      greeting: "Welcome!"
      catchphrase: "The  best  app.  Ever."

  users:
    index:
      title: "All  Users"
    show:
      title: "My  Dashboard" 
```

Enter fullscreen mode Exit fullscreen mode

现在当你在你的视图文件中时，你可以用一个键来引用一个翻译。所以，如果你在`views/users/index.html.erb`中，需要标题翻译，只需使用`t('.title')`，它就会打印出`All Users`。在`views/users/show.html.erby`，`t(.title)`会给你`My Dashboard`来代替。

## 结论

使用 I18n 可以做很多事情 ActiveRecord 对象的错误消息、复数化、本地化的日期/时间等——这只是冰山一角，但是希望这篇文章可以帮助您开始一些基本的设置:)查看下面的参考资料以获得更多内容。

### 资源

[Rails Guides](https://guides.rubyonrails.org/i18n.html)
[你需要的最后一个 Rails I18n Guides](https://phraseapp.com/blog/posts/rails-i18n-guide/)