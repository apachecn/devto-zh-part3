# 更容易使用 Rails 5.2 凭证和特定于应用程序的配置

> 原文：<https://dev.to/svyatov/easier-usage-of-rails-52-credentials-and-app-specific-configuration-461g>

众所周知，Rails 5.2 引入了一个名为 [`Credentials`](https://github.com/rails/rails/pull/30067) 的新特性。DHH 在公关中说了以下的话:

> 这个新文件是一个平面格式，不像 secrets.yml 那样被环境分割。大多数情况下，这些凭证只与生产相关，如果有人确实需要为不同的环境复制一些密钥，可以手动完成。

现在你应该知道了，Rails 6.0 [解决了](https://github.com/rails/rails/pull/33521)这个明显的不便。

同时，Rails 5.2 的一个常见解决方案是手工添加环境，大概是这样的:

```
development:
  facebook_app_id: '...'
  facebook_app_secret: '...'
  facebook_app_namespace: '...'
  stripe_publishable_key: '...'
  stripe_secret_key: '...'
  stripe_signing_secret: '...'

production:
  facebook_app_id: '...'
  facebook_app_secret: '...'
  facebook_app_namespace: '...'
  stripe_publishable_key: '...'
  stripe_secret_key: '...'
  stripe_signing_secret: '...' 
```

然后这样用:

```
Rails.application.credentials[Rails.env.to_sym][:facebook_app_secret] 
```

相当长，可读性不强，对吧？是的，你可能不会在你的应用程序中的很多地方使用凭证，但是。这一行有 47 个关于 Rails 的字符，只有 22 个关于您需要什么的字符。在我看来，这不是最好的比例。

我想向您展示的解决方案相当明显，但我从未在任何地方看到有人提出过它(可能我搜索得不够正确，如果是这样的话，对不起)。

只需打开`config/application.rb`并添加`credentials`方法，就像这样:

```
require_relative 'boot'

require 'rails/all'

# Require the gems listed in Gemfile, including any gems
# you've limited to :test, :development, or :production.
Bundler.require(*Rails.groups)

module YourApp
  class Application < Rails::Application
    # Initialize configuration defaults for originally generated Rails version.
    config.load_defaults 5.2

    # Settings in config/environments/* take precedence over those specified here.
    # Application configuration can go into files in config/initializers
    # -- all .rb files in that directory are automatically loaded after loading
    # the framework and any gems in your application.
  end

  def self.credentials
    @credentials ||= Rails.application.credentials[Rails.env.to_sym]
  end
end 
```

让我们看看它有什么变化:

```
# Before:
Rails.application.credentials[Rails.env.to_sym][:facebook_app_secret]

# After
YourApp.credentials[:facebook_app_secret] 
```

这种方法抽象了 Rails 特有的细节，使其简洁、有表现力，并且与您的应用程序相关。另外，它给了你很大的灵活性:你可以用别的东西替换 Rails 凭证，你可以合并不同地方的凭证，你可以通过删除`[Rails.env.to_sym]`部分轻松升级到 Rails 6 凭证，等等。

好的，这很好，但是帖子的标题也提到了“特定于应用程序的配置”，对吗？正确！我想再向您展示一个可以让您的 Rails 生活变得更简单的东西。

随着您的 Rails 应用程序迟早会增长，您将需要一个地方来存储应用程序的各种特定于环境的细节:不是秘密或凭证的东西，只是一些自定义配置。Rails 文档中甚至有一节使用了这个确切的名称:[自定义配置](https://guides.rubyonrails.org/v5.2/configuring.html#custom-configuration)。

根据文档，Rails 为我们提供了两个非常强大的选项:

1.  `config.x`命名空间
2.  `config_for`方法

整洁！我们使用上面描述的凭证方法并结合这两个选项怎么样？

第一步:创建`config/app.yml`

```
shared: &shared
  facebook_url: 'https://www.facebook.com/mysite'
  twitter_url: 'https://twitter.com/mysite'

development:
  <<: *shared
  domain: 'localhost:3000'
  devise_mailer_sender: 'no-reply@localhost'
  orders_mailer_sender: 'no-reply@localhost'

production:
  <<: *shared
  domain: 'mysite.com'
  devise_mailer_sender: 'no-reply@mysite.com'
  orders_mailer_sender: 'orders@mysite.com' 
```

第二步:从`config/application.rb`中的`app.yml`开始填写`config.x`

```
# App specific configuration
config.x = config_for(:app).with_indifferent_access 
```

第三步:在`config/application.rb`文件
中添加`config`方法

```
 def self.config
    @config ||= Rails.configuration.x
  end 
```

所以您的`config/application.rb`文件现在看起来像这样:

```
require_relative 'boot'

require 'rails/all'

# Require the gems listed in Gemfile, including any gems
# you've limited to :test, :development, or :production.
Bundler.require(*Rails.groups)

module YourApp
  class Application < Rails::Application
    # Initialize configuration defaults for originally generated Rails version.
    config.load_defaults 5.2

    # Settings in config/environments/* take precedence over those specified here.
    # Application configuration can go into files in config/initializers
    # -- all .rb files in that directory are automatically loaded after loading
    # the framework and any gems in your application.

    # App-specific configuration
    config.x = config_for(:app).with_indifferent_access
  end

  def self.config
    @config ||= Rails.configuration.x
  end

  def self.credentials
    @credentials ||= Rails.application.credentials[Rails.env.to_sym]
  end
end 
```

我们可以像这样使用我们的自定义配置:

```
YourApp.config[:devise_mailer_sender] 
```

多酷啊。:)