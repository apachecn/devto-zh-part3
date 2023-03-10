# 将 Omniauth GitHub 添加到 Devise 之上的 Rails 应用程序中

> 原文：<https://dev.to/mercier_remi/add-omniauth-github-to-your-rails-app-on-top-of-devise-5n7>

去年 6 月，当我已经进入最后三周的 [@lewagon 的 ruby bootcamp](http://lewagon.com/) 时，我和我的队友们在进入我们的最终项目之前正在开发市场应用程序。

授权人们注册 GitHub 的需求很快出现。这里有一个循序渐进的教程，介绍我在使用 Devise 处理认证时是如何做到的。

希望你会喜欢它，并发现这很有帮助。🙏

## 基本设置完毕

在使用你的应用程序之前，你需要设置一些东西。

### 在 Git Hub 上创建应用

首先，我们需要在 GitHub 上创建一个 OAuth 应用程序。转到您的个人资料，然后点击`developper settings`并点击`New OAuth App`。

给你的应用一个名字，一个 URL 和一个回调 URL。如果您想使用 localhost 呢？很高兴你问了。

对于您的敬意网址:`http://http://localhost:your_port`将完美地工作

对于回调 URL，不能使用`http://http://localhost:your_port`，因为 GitHub 需要你指定一个可以公开访问的地址。那么，我们该怎么办？

### n 拯救世界

ngrok 通过公共 URL 公开您的本地 web 服务器。所以我们需要安装 ngrok 并让它监听我们的本地服务器。然后，我们将能够获得一个公共网址，并把它给 GitHub。

首先，在您的终端中运行:

```
brew install ngrok 
```

然后运行:

```
ngrok http your_port 
```

你会得到:

```
ngrok by @inconshreveable                                       (Ctrl+C to quit)

Session Status                online
Session Expires               7 hours, 59 minutes
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://3aa8f0f7.ngrok.io -> localhost:3000
Forwarding                    https://3aa8f0f7.ngrok.io -> localhost:3000

Connections                   ttl     opn     rt1     rt5     p50     p90
                 0       0       0.00    0.00    0.00    0.00 
```

这就是你的回调 URL: `http://3aa8f0f7.ngrok.io -> localhost:3000`。

不要忘记在你的回拨 URL 后面加上`/users/auth/github/callback`。

您的开发回调 URL 应该如下所示:

`http://3aa8f0f7.ngrok.io/users/auth/github/callback`

一旦进入生产阶段，我们将需要更改这两个 URL。⚠️注意在你的应用程序 URL 和回拨 URL 中使用相同的协议。在一个中使用`http`,在另一个中使用`https`,会导致 URL 不匹配。

### 在你的 Rails 应用中保存你的 API 密钥

我们不希望我们的 API 密钥被推送到 GitHub。所以我将使用 [figaro gem](https://github.com/laserlemon/figaro) 将这些存储在一个安全文件中。(注意:您也可以使用。env 文件)。

```
# Add figaro to your gem file@
gem 'figaro' 
```

```
# In your terminal
bundle install 
```

Figaro 创建了一个 *config/application.yml* 来放置所有的 API 键，并将这个文件添加到. gitignore 中。

将您的密钥复制/粘贴到*config/application . yml*
中

```
development: GITHUB_ID: 8***********************b
  GITHUB_SECRET: 4***********************************************3 
```

不要忘记告诉 devise 使用*config/initializer/device . Rb*中的这些键。

```
config.omniauth :github, ENV['GITHUB_ID'], ENV['GITHUB_SECRET'], scope: 'user,public_repo' 
```

## 用 Devise 配置 Omniauth GithHub

因为我们使用 Devise 来处理认证，所以我们不想在*config/initializer/omni auth . Rb*中写 config。

我们言归正传吧！

第一步是将 Omniauth gem 安装到您的应用程序中。转到您的`Gemfile` :

```
gem 'omniauth-github' 
```

然后运行:

```
bundle install 
```

下一步是将`provider`和`uid`列添加到我们的用户模型中。记住我们已经在我们的*配置/initializer/device . Rb*中声明了提供者。

```
rails g migration AddOmniauthToUsers provider:string uid:string
rake db:migrate 
```

我们转到`user.rb`并让我们的用户无所不能。

```
devise :omniauthable, omniauth_providers: %i[github] 
```

有了`devise_for :users`之后，Devise 将创建两个 URL 方法:

*   `user_omniauth_authorize_path(provider)`
*   `user_omniauth_callback_path(provider)` _

### 将其添加到您的视图中

粘贴下面的代码得到一个简单的链接来测试它:

```
<%= link_to "Sign up with GitHub", user_github_omniauth_authorize_path %> 
```

⚠️传递给`user_omniauth_authorize_path`方法的符号应该与传递给 devise 的配置块的提供者的符号相匹配。

现在，当点击`Sign up with GitHub`时，人们将被重定向到 GitHub 以给出他们的凭证。但就目前而言，当 GitHub 发回用户的数据时，什么也没有发生。

### 将回拨添加到您的应用程序

让我们回到我们的`config/routes.rb`来告诉 Devise 我们将在哪个控制器中实现我们的回调:

```
devise_for :users, controllers: { omniauth_callbacks: 'users/omniauth_callbacks' } 
```

现在我们只添加文件`app/controllers/users/omniauth_callbacks_controller.rb` :

```
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
end 
```

回调应该与我们在 Devise 的配置块中传递的提供者同名。

```
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
   def github
       @user = User.from_omniauth(request.env["omniauth.auth"])
       if @user.persisted?
         sign_in_and_redirect @user, event: :authentication #this will throw if @user is not activated
         set_flash_message(:notice, :success, kind: "GitHub") if is_navigational_format?
       else
         session["devise.github_data"] = request.env["omniauth.auth"]
         redirect_to new_user_registration_url
       end
     end

     def failure
       redirect_to root_path
     end
 end 
```

然后，从控制器，我们移动到我们的用户模型。下面是来自 Devise 文档的代码:

```
def self.from_omniauth(auth)
   where(provider: auth.provider, uid: auth.uid).first_or_create do |user|
     user.email = auth.info.email
     user.password = Devise.friendly_token[0,20]
     user.name = auth.info.name   # assuming the user model has a name
     user.image = auth.info.image # assuming the user model has an image
     # If you are using confirmable and the provider(s) you use validate emails,
     # uncomment the line below to skip the confirmation emails.
     # user.skip_confirmation!
   end
 end 
```

在修改了上面的代码以适应我的主要需求之后，我遇到了几个问题:

*   我使用 Carrierwave 在 Cloudinary 上上传图片，所以我需要给这个方法添加适当的逻辑。
*   如果一个用户已经存在于我的数据库中，并试图用 GitHub 登录，他会被重定向到登录而不登录。

因此，这里是我自己的基于我的模式和需求的类方法版本:

```
def self.from_omniauth(auth)
     user = User.find_by(email: auth.info.email)
     if user
       user.provider = auth.provider
       user.uid = auth.uid
       user.save
     else
       user = User.where(provider: auth.provider, uid: auth.uid).first_or_create do |user|
         user.email = auth.info.email
         user.password = Devise.friendly_token[0,20]
         user.first_name = auth.info.name.split(' ').first
         user.last_name = auth.info.name.split(' ').second
       end
     end
     unless user.avatar.present?
       photo_url = auth.info.image
       user.remote_avatar_url = photo_url # Carrierwave helper
       user.save
     end
     user
   end 
```

导致第二个问题的原因是模型有两个相互冲突的验证策略:

*   设计
*   和 Rails 原生的`validates`方法。

我的用户模型有一个`validates :password, presence: true`，它引发了一个错误并阻止用户登录。这一行无用的代码是由我们团队内部缺乏沟通造成的。彼此多交流可以让我们省去很多调试☝️.的麻烦

## 推入生产

在测试并合并了我在 GitHub 上的分支后，我们把它推到了 Heroku。在这一点上，重要的是回到你的 GitHub 应用页面，用你的域名更新应用 URL 和回调 URL。

您的应用程序 URL 现在应该如下所示:

主页 URL: `http://your_app_url.your_domain`

回拨网址:`http://your_app_url.your_domain/users/auth/github/callback`

还有，别忘了给 Heroku 你的 API 密匙。否则你的生产环境将不知道如何解释你的*config/initializer/device . Rb*中的`config.omniauth :github, ENV['GITHUB_ID'], ENV['GITHUB_SECRET'], scope: 'user,public_repo’`(你的用户将得到一个华丽的 404)。

别忘了跑:

```
heroku run bundle # to install the omniauth gem
heroku run rails db:migrate # to update your users schema
heroku restart 
```

现在，人们可以使用他们的 GitHub 凭据注册并登录到您的应用程序。🙌