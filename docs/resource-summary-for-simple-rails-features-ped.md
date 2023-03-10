# 简单 Rails 特性的资源摘要

> 原文：<https://dev.to/micahshute/resource-summary-for-simple-rails-features-ped>

## 上周，我制作了我的第一个 rails 应用程序，这里是我用来让它工作的一些资源和策略

### 谷歌认证

*   [谷歌 OAuth gem](https://github.com/zquestz/omniauth-google-oauth2)

```
# config/initializers/omniauth.rb

Rails.application.config.middleware.use OmniAuth::Builder do
    provider :google_oauth2, ENV['GOOGLE_KEY'], ENV['GOOGLE_SECRET']
end 
```

*   如果您需要一个惟一的属性来查询用户，或者想要使用 Google API 的更多高级特性(并允许离线刷新令牌)，您可以向`user`表添加一个`google_uid`列和一个`google_refresh_token`。因为我对用户的`email`进行了唯一性验证，并且我只使用 Google 进行认证，所以我不需要这些列，但是还是添加了它们，以防将来设计发生变化。
*   在我的模式中，我让一个`user`的`email`验证唯一性，这样用 Google 登录就可以创建一个全新的账户，或者找到一个有那个电子邮件的账户，在不修改密码的情况下更新一个`google_uid`属性。
    *   为了使这种方法万无一失，如果帐户是传统的(即没有 OAuth)，让用户验证他们的电子邮件是很重要的。
*   您需要一个专门处理 OAuth 回调的操作。
    *   这个动作需要与您在 Google API 页面中指定的回调路由相关联(该页面的链接在上面链接的 Google OAuth gem 的 GitHub 上。)
    *   我在我的`routes.rb`文件中定义的回调的自定义路由是:`get 'auth/google_oauth2/callback', to: 'sessions#googleAuth'`
    *   我在`SessionsController`中的回调操作利用了在`User`模型中编写的自定义类方法，它将 OAuth 进程的返回传递给该方法，如下所示:

```
#  SessionController#googleAuth
    access_token = request.env["omniauth.auth"]
    user = User.from_google(access_token)

#  User.rb

    def self.from_google(auth)
        refresh_token = auth.credentials.refresh_token
        if (found_user = User.find_by(email: auth.info.email))
            found_user.google_uid = auth.credentials.token
            found_user.google_refresh_token = refresh_token if refresh_token.present?
            return found_user
        else
            new_user = User.new do |u|
                u.email = auth.info.email
                u.name = auth.info.name
                u.google_uid = auth.credentials.token
                u.google_refresh_token = refresh_token if refresh_token.present?
                rand_password = RandomPasswordStrategy.random_password
                u.password = rand_password
                u.password_confirmation = rand_password
            end
            return new_user
        end

    end 
```

*   在那里，您只需检查用户是否有效，如果有效，就让他们登录，否则显示一个错误。
*   `RandomPasswordStrategy`只是我创建的一个类，它使用`sysrandom/securerandom`方法`SecureRandom.hex(64)`结合所需符号和数字的随机排序(根据密码策略的要求)来创建一个复杂、有效的随机密码。

### 授权方式，错误页面

*   我在我的`ApplicationController`中放了很多私有方法来抽象整个应用程序中的授权，这样更容易以正确的方式验证用户，并在出现问题时向他们显示适当的消息。
*   我希望注销的用户能够看到应用程序的大部分，然后限制它的其他部分，只有帐户的用户。有些方面只有特定用户可以访问，例如编辑您自己的帖子等。
*   根据用户是否登录，有些页面会有不同的显示。为了简单地实现这一点，我向`ApplicationController`添加了两个方法，以便它对所有控制器(继承自`ApplicationController`)都可用，同时添加它们作为`helper_methods`，以便在必要时使它们在视图中可用:

```
#  application_controller.rb

helper_method :logged_in?
helper_method :current_user

private

def current_user
    User.find_by(id: session[:user_id])
end

def logged_in?
    !current_user.nil?
end 
```

*   这些主要用于决定在某些页面上显示什么，或者构建更高级的授权方法。
*   如果未经授权的用户试图访问您不希望他们访问的页面，则显示 403 错误页面是合适的。我通过创建一个`public/403.html.erb`文件，并通过`ApplicationController`中的另一个方法调用它来完成这个任务

```
# application_controller.rb

def not_authorized(msg = "You are not authorized to view that page")
    flash[:danger] = msg
    render(:file => File.join(Rails.root, 'public/403.html.erb'), :status => 403, :layout => false)
end 
```

*   它可以接受一个自定义的 flash 消息作为参数，并且可以作为其他方法的一个很好的构造块，比如:

```
# application_controller.rb

def authorize(user=nil)
    if user.nil?
        not_authorized('<a href="/login">Login</a> or <a href="/signup">Signup</a> to view this page!') unless logged_in?
        !!current_user
    else
        if user == current_user
            not_authorized
            false
        else
            true
        end
    end
end 
```

*   如果在没有用户参数的情况下调用上面的代码，那么在用户没有登录的情况下，它只会显示一个 403 错误页面。如果它是由用户调用的，403 将被调用，除非该特定用户已登录。它还返回一个布尔值来指示成功或失败。如果调用`authorize`后需要在控制器中使用`current_user`，授权失败会报错。解决这个问题的一个简单方法是使用一个带有授权检查的`if-else`块，而不是把它作为一个独立的函数，这样任何调用 current_user 的代码都不会运行，除非授权成功。为此，必须返回一个布尔值(或者至少是 true 和 falsy)。如果授权失败，它将返回 false，然后一旦控制器操作完成，它将呈现 403 错误页面。

### 关注用户，发送消息，回复帖子，范围方法

对于某些关系，ActiveRecord 关系并不像`has_many :classes, through: :user_classes`或类似的东西那么简单。特别是 3 种情况，我不得不变得更有创造力:

#### 跟随其他用户:

*   多对多关系，即一个用户关注许多用户，同时也被许多用户关注。
*   这个问题的答案只是一个简单的连接表，但它与标准的连接表略有不同，因为它将一个表连接到自身，并且在 ActiveRecord 类方法中需要一些额外的单词。我使用的连接表是:

```
# schema.rb

create_table "following_users", force: :cascade do |t|
    t.integer "following_id"
    t.integer "follower_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
end 
```

*   然后为了使用户模型正确工作:

```
# user.rb

has_many :following_users, foreign_key: "follower_id", dependent: :destroy
has_many :follower_users, class_name: "FollowingUser", foreign_key: "following_id", dependent: :destroy
has_many :followers, class_name: "User", through: :follower_users, foreign_key: "follower_id"
has_many :following, class_name: "User", through: :following_users, foreign_key: "following_id" 
```

#### 消息传递

*   这与实现 following/followers 非常相似，只是模型`Message`是连接表，也是我们感兴趣的模型，所以它只是:

```
# schema.rb

create_table "messages", force: :cascade do |t|
    t.integer "sender_id"
    t.integer "reciever_id"
    t.text "content"
    t.boolean "viewed", default: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
end 
```

```
# user.rb

has_many :sent_messages, :class_name => "Message", :foreign_key => "sender_id", dependent: :destroy
has_many :recieved_messages, :class_name => "Message", :foreign_key => "reciever_id", dependent: :destroy 
```

#### 对帖子做出反应

*   我考虑过这个问题的几个解决方案，但是我决定制作一个表`ReactionType`，它与任何为`reactable`的事物有多对多的多态关系，在我的例子中`Topic` s 和`Post` s. `Reactions`是`reactables`和`ReactionTypes`之间的连接表。我这样做是为了减少错误(即拼写错误),并允许灵活性，例如，将来容易扩展 allowed `ReactionTypes`,而不是通过字符串保存反应类型。
    *   这样做使得`Reactions`成为了`User`、`reactables`和`ReactionType`之间的 3 路连接表
    *   必须植入您希望用户可以使用的类型。我有了`like`、`dislike`、`genius`和`report`。

```
# schema.rb

  create_table "reaction_types", force: :cascade do |t|
    t.string "name"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "reactions", force: :cascade do |t|
    t.integer "user_id"
    t.string "reactable_type"
    t.bigint "reactable_id"
    t.integer "reaction_type_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    t.index ["reactable_type", "reactable_id"], name: "index_reactions_on_reactable_type_and_reactable_id"
  end 
```

```
# reaction.rb

  belongs_to :user
  belongs_to :reaction_type
  belongs_to :reactable, polymorphic: true 
```

```
# post.rb

has_many :reactions, as: :reactable, dependent: :destroy
has_many :reaction_types, through: :reactions

#topic.rb

has_many :reactions, as: :reactable, dependent: :destroy
has_many :reaction_types, through: :reactions 
```

*   以同样的方式使用多态关系，您可以创建可以附加到`taggables`的标签，并发布对属于`postable`的事物的回复。

#### 高级 ActiveRecord 类方法

*   当我试图创建正确的 ActiveRecord 类方法来为用户仪表板检索特定的“统计数据”或为我的主页查找热门话题时，我发现对我来说最好的资源是查看其他高级和特定的类方法，并使用这些示例作为参考。我将我的一些放在这里，希望其他人可以作为参考。
    *   为我的主页创建一天的“焦点话题”——选择过去 24 小时内反应最积极的话题:

```
# topic.rb

def self.trending_today
    Topic.joins(:reaction_types).where(reaction_types: {name: ['like', 'genius']}, classroom_id: nil).where(reactions: { created_at: ((Time.now - 24 * 3600)..Time.now)}).group('id').order(Arel.sql('count(reaction_type_id) DESC')).limit(1)
end 
```

*   找到`User`和`reaction_type`反应最强烈的`Topic`:

```
#topic.rb

def self.most_liked_by_user(user, limit=5)
    most_reacted_type_by_user(user, 'like', limit, false)
end

private

def self.most_reacted_type_by_user(user, type, limit, count)
    if count
        Topic.joins(:reaction_types).where(reaction_types: {name: type}, user_id: user.id).group('id').order(Arel.sql('count(reaction_type_id) DESC')).limit(limit).count
    else
        Topic.joins(:reaction_types).where(reaction_types: {name: type}, user_id: user.id).group('id').order(Arel.sql('count(reaction_type_id) DESC')).limit(limit)
    end
end

#user.rb

def most_liked_topics(limit=5)
    Topic.most_liked_by_user(self, limit)
end 
```

### 设置引导程序

*   [这篇文章](https://medium.freecodecamp.org/add-bootstrap-to-your-ruby-on-rails-project-8d76d70d0e3b)将在几分钟内介绍如何用 bootstrap 设置你的 rails 应用。
*   bootstrap 有一个复杂之处，它不是理想的“开箱即用”->当表单输入有问题时，rails 会自动给你的`form_for`字段一个`fields_with_errors`类，Bootstrap 不会响应它。相反，它响应的是`is-invalid`这个类别。
    *   [这个博客](https://jasoncharnes.com/bootstrap-4-rails-fields-with-errors/)展示了一个很好的解决方案，可以让表单错误在 rails 中很好地工作。它只是包括添加一个文件和代码到你的`config/initializers`文件夹，基本上，它所做的只是将默认的`field-with-errors`类改为`is-invalid`，这是 bootstrap 用来指示一个没有正确填写的字段。

### 渲染降价

*   我在两个图书馆`redcarpet`和`kramdown`之间。我最终选择了[卡姆登](https://github.com/gettalong/kramdown)，因为:
    *   它是最近提交的，似乎维护得更多
    *   它的受欢迎程度在上升，而`redcarpet`却在下降
    *   它允许与允许乳胶渲染的`MathJax`集成。
*   获得基本的集成相对简单。实现以下两个目标变得有点复杂:

    *   代码的语法突出显示
    *   能够净化用户输入，同时允许所有降价功能正确实现。
*   我决定使用[胭脂](https://github.com/jneen/rouge)来突出语法。我将向您展示下面的代码，让它与 Kramdown 一起工作，但是很难找到的信息是如何获得适当的 CSS 文件来使高亮显示发生。这里有一个好的[栈溢出答案](https://stackoverflow.com/questions/43905103/kramdown-rouge-doesnt-highlight-syntax)很难找到。基本上，一旦你告诉`kramdown`你想要使用`rouge`(并且你已经安装了`kramdown gem`和`rouge gem`)，你就可以在你的终端中运行`rougify help style`，并且你可以看到所有你可以添加到你的`app/assets/stylesheets`路径中的定制 CSS 文件。要获取原始 CSS，键入`rougify style`，后跟一个允许的样式。比如:`rougify style colorful`。CSS 将在您的终端中打印。你也可以运行

```
rougify style colorful > ./app/assets/stylesheets/rouge_style.css 
```

*   现在，渲染降价相对容易。我创建了一个助手方法来使它变得非常简单:

```
# application_helper.rb

def render_markdown(markdown)
    render 'components/content', markdown: Kramdown::Document.new(markdown, parse_block_html: true, syntax_highlighter: :rouge, syntax_highlighter_opts: {line_numbers: false}).to_html
end 
```

```
 <!-- views/components/_content.html.erb -->

<%= sanitize_markdown markdown %> 
```

*   自定义消毒方法:
    *   正如你在这里看到的，我没有使用`raw`或`.html_safe`来呈现这个 HTML 数据，因为最终它*来自一个用户，不能被信任*。然而，安全的`sanitize`方法不允许我想要呈现的某些东西，比如表格。通过手动添加到`sanitize`允许的白名单标签中，您可以获得适当的卫生和您想要的 HTML 标签，如下所示。

```
# application_helper.rb

def sanitize_markdown(content)
    sanitize(content, tags: Loofah::HTML5::WhiteList::ALLOWED_ELEMENTS_WITH_LIBXML2.to_a + %w(table th td tr span), attibutes: Loofah::HTML5::WhiteList::ALLOWED_ATTRIBUTES + %w( style ))
end 
```

### 一般提示

*   使用 PostgreSQL 设置应用程序
    *   这里有一篇我写的关于设置 WSL 的博客文章，但是在底部有一节介绍了如何使用 pgAdmin 和使用 PostgreSQL 设置 Rails 应用程序
*   将所有密钥保存为环境变量。我使用了 [Figaro](https://github.com/laserlemon/figaro) 来简化它。
*   避免 N+1 问题
    *   当您获得一个模型集合，并希望查询和显示其中的嵌套模型时，就会出现这种情况。如果你在你的模型上迭代`n`次来做这件事，你正在进行`n+1`数据库调用，这对于大量的数据来说是非常昂贵的，特别是当通过网络请求时。在进行初始查询时，使用`includes`方法可以很容易地解决这个问题。[查看](https://guides.rubyonrails.org/active_record_querying.html)网站，并前往“N + 1 查询问题的解决方案”部分了解更多信息。
*   当对象被销毁时自动清理数据库
    *   当你适当地添加

```
 dependent: :destroy 
```

对于您的 ActiveRecord 类方法(示例可以在上面的代码片段中看到)，您是在告诉 ActiveRecord 在破坏模型时破坏这些关系。正如你在上面看到的，我在`post.rb`中为`reactions`实现了这个。这告诉 rails 当一个`post`被销毁时，它的所有用户`reactions`也应该被销毁。这可以防止不相关的喜欢和不喜欢等在你的数据库中毫无理由地浮动。另外，请注意这应该是一种单方面的关系。如果用户破坏了他们对它的一个反应，你不会希望一个`post`被破坏。

*   添加自定义文件和类
    *   你希望你的应用程序做一些 MVC 之外的事情。这意味着你会想要在标准的`model`、`view`和`controller`目录之外添加文件。以下是一些关于如何做到这一点的建议资源:
    *   [一个 StackOverflow 答案](https://stackoverflow.com/questions/15260984/guidelines-for-where-to-put-classes-in-rails-apps-that-dont-fit-anywhere)
    *   [一篇 GitHub gist 帖子](https://gist.github.com/maxim/6503591)
*   就我个人而言，我在我的`app`目录中使用了一个`lib`目录，因为它在生产中被急切地加载，而在开发中被缓慢地加载。我不需要为该目录中被引用的文件中的类修改任何配置或环境文件。