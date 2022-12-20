# 铁轨！我来了(第五天)

> 原文：<https://dev.to/saral/rails-here-i-come-day5-2a7l>

我本应该在昨天，也就是 2 月 1 日，写下这篇文章，但是，即使我昨天已经写好了代码，我还是没有勇气在周五晚上写这篇文章。我需要一些休息时间。所以我在一个周六的下午充好电，准备发帖。

首先，在第四天，我经历了为用户注册和登录建立认证的真正斗争。我试图理解和阅读代码，因为我是从一个教程的功能编程。然而，有些时候我不知道教程在说什么。我试图按照教程，但无济于事。就在那时，我想，由于我对 Ruby 缺乏了解，我没有理解这个教程。如果我想完全理解代码，我需要升级我的 Ruby 游戏。于是我报了一个 Ruby 的课程，真正开始学习 Ruby。在听了 code newbie 上的一个播客后，我找到了 T2 的 the Odin project。这是我在学习用 Ruby 编码时决定坚持的。

有了这种认识，并采取了行动，我决定继续构建我正在从事的第一个博客项目。特别是，我想完成用户注册和登录页面。

以下是我的成就以及我是如何做到的。

1.  使用 gem 'bcrypt '

    这是我在开始创建页面之前学到的东西。试图从头开始做所有事情就像试图重新发明轮子，尤其是作为一个没有 Ruby 坚实基础的初学者，从头开始会更加困难。因此，我使用了宝石“bcrypt”。这个宝石在我的 gem 文件中是可用的，我需要做的就是取消它的注释。然后在运行了`bundle install`并重启了我的服务器后，我准备好了。然后，我修改了模型文件夹中的 user.rb 文件。

*user.rb*

```
class User < ApplicationRecord

  has_secure_password
  validates :email, uniqueness: true, presence: true
end 
```

在创建我的用户数据库时，我仅仅运行了`rails g model User email password_digest`。如您所见，现在还没有设置密码和密码确认。使用 bcrypt，我们能够通过 has_secure_password 方法向用户模型添加密码和密码确认。此外，该方法还检查验证。我可以通过编写 validation: false 来自己处理验证，但是现在，我不想走这条路。我知道这是可以做到的，但是为什么要重新发明轮子呢？

1.  设置路线

*routes . Rb*T2】

```
Rails.application.routes.draw do
  # Routes for users
  root 'dashboard#show' 
  get 'users/new' => 'users#new', as: 'register_page'
  get 'sessions/new' => 'sessions#new', as:'login'
  get 'sessions/destroy' => 'sessions#destroy', as: "logout"

  resources :sessions, only: [:create, :new, :destroy]
  resources :users, only: [:new]

  #Routes for blog posts

  post 'posts' => 'posts#create'
  get 'posts/new' => 'posts#new', as: 'new_post'
  get 'posts/failure' => 'posts#failure', as: 'failure'
  get 'posts/:id' => 'posts#show' , as: 'post'

end 
```

正如所见，我有博客文章和用户的路线。我选择为会话和用户创建资源。然而，我没有创建 Rails 提供的所有默认资源，而是选择了我需要的资源(控制器的动作)。有人建议我只获得我需要的资源，而不是一切。我需要的资源是会话和用户的。会话将允许我存储用户会话数据，换句话说，用户是否登录。此外，它会有用户登录页面。用户允许我创建一个新用户，创建动作允许我将用户数据存储在我创建的用户数据库中。

1.  设置控制器

接下来是为路线设置控制器。首先是应用控制器。

*application _ controller . Rb*

```
class ApplicationController < ActionController::Base
    before_action :require_valid_user!

    def current_user
        if !session[:user_id].blank?
        @user ||= User.find(session[:user_id])
        end
    end

    def require_valid_user!
        if current_user.nil?
            flash[: error] = 'You must be logged in to access that page!'
            redirect_to login_path
        end
    end
end 
```

我真的按照这里的教程做了，但也试图理解到底发生了什么。这是我的阅读材料。第一行定义了 before_action。这种情况下是 require_valid 用户。require_valid_user 然后被定义。它检查当前用户是否为零。如果为 nil，它会显示一个错误并将用户重定向到 login_path。还定义了当前用户。基本上就是说，如果 session user_id 为空，那么搜索用户数据库，找到 session_id。这意味着如果会话有一个 user_id，那么它将存储在实例@user 中，如果没有，则@user 返回 nil。如果为空，如 require_valid_user 中所定义的，用户将被重定向到登录路径。有了这个设置，我们继续到用户控制器。在这里，我需要注册过程发生。用户将通过注册表单注册，数据将保存在用户数据库中。

*users_controller.rb*

```
users_controller.rb
class UsersController < ApplicationController

  skip_before_action :require_valid_user!
  before_action :reset_session

  def new
    @user = User.new
  end

  def create
    @user = User.new(user_params)

    if @user.save
      flash[: success] =  'You have successfully created an account.  Please sign in to continue.'
      redirect_to login_path
    else
      render :new
    end
  end

  private
    def user_params
      params.require(:user).permit(:email, :password, :password_confirmation)
    end
end 
```

在用户控制器中，已经定义了动作 new 和 create。记住，我们已经在 routes.rb 页面中创建了 new 和 create using 资源。这里我们首先要求控制器跳过 before_action。这意味着用户不需要登录来进行注册。有道理，对吧？

现在，我们定义一个新动作。这里，我们将从注册页面的表单中获取的值存储在 instance @user 中。所有的验证都是通过`bcrypt` gem 完成的。接下来，我们创建一个动作，检查用户是否保存在数据库中，如果保存了，用户会收到一条消息，并被重定向到一个登录页面，用户可以从这里登录。这里，存储过程类似于我们为博客文章存储数据的方式。

注册完成后，我们将注意力集中在通过会话控制器登录和注销上。

*sessions _ controller . Rb*

```
class SessionsController < ApplicationController
  skip_before_action :require_valid_user!, except: [:destroy]

  def new
  end

  def create
    reset_session
    @user = User.find_by(email: session_params[:email])

    if @user && @user.authenticate(session_params[:password])
      session[:user_id] = @user.id
      flash[:success] = 'Welcome back!'
      redirect_to root_path
    else
      flash[:error] = 'Invalid email/password combination'
      redirect_to login_path
    end
  end

  def destroy
    reset_session
  end

  def session_params
    params.require(:session).permit(:email, :password)
  end

end 
```

会话控制器处理我们的登录和注销操作。创建操作是处理登录的操作。它根据在登录页面中输入的电子邮件找到用户，并检查所有必需的条件，如数据库中是否存在用户以及密码是否匹配。销毁操作会重置会话，用户可以注销。就我个人而言，这里还有一些东西需要我解开，尤其是 session_params 和这里正在发生的事情。但是现在，我不想在这上面陷得太深，我会在我的 Ruby 课上学到更多。

1.  设置视图

注册和登录的视图与新文章页面的视图相似。它们都由表单组成，这些表单被提交给数据库。我很高兴做的一个更新是动态导航条，如果用户登录或者没有用户，它会改变，我通过
改变`application.html.erb`来实现这个。

```
<ul class="nav navbar-nav navbar-right">        
         <%  if !session[:user_id]%>
            <li><%= link_to 'Login', login_path , class: 'glyphicon glyphicon-log-in'%></a></li> 
            <li><%= link_to 'Register', register_page_path , class: 'glyphicon glyphicon-user'%></li>             
          <% else %>         
          <li><%= link_to 'Logout', logout_path , class: 'glyphicon glyphicon-log-out' %> <li>

        <% end %> 
```

最后，注册和登录过程完成了，这是对我走到这一步的鼓励。我还想添加更多的功能，并将在未来的日子里继续添加。此外，我还得感谢[这个教程](https://richonrails.com/articles/building-an-authentication-system-from-scratch)给了我很大的帮助。

接下来，我计划在 heroku 上部署应用程序。我现在不知道该怎么做，但是我会想出办法的。有一件事已经让我有点不舒服，那就是我使用 sqlite3 作为我的数据库，很明显，heroku 不支持它。所以我必须找到一种使用 Postgresql 的方法，鉴于我有限的数据库知识，我有点不知所措，但我们会解决它的。耐心。