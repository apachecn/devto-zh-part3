# RAILS 最终项目:我知道了！(五. 2)

> 原文：<https://dev.to/emiko/rails-final-project-i-have-that-v-2-1od4>

起草

# RAILS 最终项目:我有那个！(五. 2)

对于我的 Rails 最终项目，我决定转换我的 Sinatra 最终项目应用程序，我有！，并添加我希望在使用 Sinatra 制作这个应用程序时添加的功能。

我有那个！是一个社区借阅应用程序，允许用户注册并列出他们想要借给他们社区的物品，并从可用物品列表中借阅物品。我创建这个应用程序的希望是，通过共享我们已经拥有的资源，帮助培育一个更可持续的世界，并与碰巧生活在你所在地区的愿意做同样事情的人创建社区。这款应用的 Sinatra 版本没有用户的位置属性，因此用户无法按位置过滤项目列表。这是我想添加到这个应用程序的 Rails 版本的一个功能。我想添加的另一个功能是能够让每个项目属于一个类别，并按类别列出项目，而不是有一个大的项目列表。

为了构建这些功能，我首先需要决定我的模型及其关系。我用笔和纸做了这个，用它们的属性制作模型，并决定哪些模型将有一个所属关系，哪些将有 has_many 和 has_many 直通关系。几经周折，我决定有一个用户模型，物品模型，和类别模型。项目模型将充当我的连接表，并具有 user_id 和 category_id 属性。用户模型应该具有:用户名，:电子邮件，:密码摘要(对于 has_secure_password)，:州和:城市属性。类别模型的属性是:name 和:description。

在我画出我的模型并确信它们之后，我开始构建我的 rails 应用程序。在我的终端中，我运行`rails new i-have-that-v2`来获取我的 rails 应用程序的文件结构。之后，我添加了我的模型，并通过运行`rails g migration name_of_my_migration`创建我的迁移文件来构建我的迁移。为了满足嵌套资源的需求，我决定将我的项目嵌套在我的用户中。这意味着不是/items/new，而是 users/:id/items/new。

我的 routes.rb 看起来是这样的:

```
Rails.application.routes.draw do
  get 'auth/:provider/callback', to: 'sessions#create'
  get 'auth/failure', to: redirect('/')
  resources :categories
  resources :users, except:[:show]
  resources :users, only:[:show] do
  resources :items 
```

因此，要创建一个新项目(通过我的嵌套资源)，我必须将用户和项目传递给表单。我花了一段时间才弄明白如何做到这一点，但这是我的项目控制器:新动作代码:

```
class ItemsController < ApplicationController
def new 
@user = find_user 
@item = Item.new(user_id: @user.id) 
end 
```

在设置了@user 和@item 之后，我将这两个变量都传递给了 item/new/html . erb 表单:

```
<%= form_for [@user, @item] do |f| %>
  <%= f.hidden_field :user_id %>
  <%= f.label :name %>
  <%= f.text_field :name %><br>
  <%= f.label :description %>
  <%= f.text_field :description %><br>
  <%= f.label :category_id, "Item Category" %>
  <%= f.collection_select(:category_id, Category.all, :id, :name, { :prompt => 'Select a Category' }) %><br>
  <%= f.label :available, "Check this box if this item is available for lending"%>
  <%= f.check_box :available %><br>
  Didn't see a category for your item? Create one <%= link_to "here:", new_category_path %><br>
  <%= f.submit %>
<% end %> 
```

在这个表单中，`f.hidden_field :user_id`是一个隐藏字段，充当项目和用户之间的连接器。提交该表单时，该字段将作为 item[:user_id]出现在参数中，其值将被指定为创建该 item 对象时@user 的 id。

在添加 OmniAuth 之前，我构建了我的所有应用程序，并确保一切正常，我很高兴我做到了这一点。这是因为我遇到的最大挑战(我在完成这个项目时遇到了很多挑战！)是让我的 OmniAuth 谷歌工作。我试着从这个教程安装 OmniAuth google。当我的 omniauth google gem 不能捆绑时，我马上就遇到了问题。在一个了不起的 TC (JENN！)，我们(我们，我指的是 Jenn)发现，要让 omniauth google 进行捆绑，我们必须在 gemfile ( `gem ‘omniauth-google-oauth2’, ‘~> 0.5.3’`)中指定它的最新版本，不像 omniauth-facebook 那样，不需要指定版本就可以捆绑。我在 OmniAuth Google 上遇到的另一个小问题是在我的 SessionsController 中不断弹出的一个错误。

错误一直说我这行代码里没有方法‘env’:@ user = user . from _ omni auth(env[" omni auth . auth "])；通过谷歌搜索，我找到了一个快速解决方法。我所要做的就是在 env:@ user = user . from _ omni auth(request . env[" omni auth . auth "])前添加' request '；。

一旦我让我的 OmniAuth 工作，我觉得我刚刚跑完一场马拉松，我带自己出去吃冰淇淋，也许我高兴得哭了！

这里是我的项目的视频演示。

感谢阅读。