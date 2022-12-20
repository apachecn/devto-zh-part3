# 弄清楚

> 原文：<https://dev.to/saral/figuring-it-out-pnn>

一天快结束的时候，我终于发现我做错了什么，或者我应该说，我根本没有做什么。所以这就是我昨天纠结的事情。

我今天做了什么不同的事情？

首先，我昨天试图通过用户名进行认证。在我的脑海中，我在想我应该做一些类似于`<%= if user.username == post.username`的事情，然后它会给用户删除或编辑他们的帖子的能力。

然而，在听了安迪·勒维伦茨教授的教程后，我明白了我需要做什么。安迪·勒维伦茨教授的教程教我如何建立一个像 T2 一样的推特应用程序。我没有尝试用用户名进行验证，而是用 user.id 进行验证。这个用户 id 将 post 数据库与用户数据库链接起来。

我是怎么做的？

首先，我通过`rails generate migration
add_userid_to_post userid: integer`在我的帖子模型中添加了一个 user_id 字段。然后在`rails db: migrate`之后，我能够在我的 post 数据库中获得字段 user.id。

之后，我对模型文件夹中的 post.rb 和 user.rb 文件进行了修改。

*post . Rb*T2】

```
class Post < ApplicationRecord
    belongs_to :user
end 
```

*User.rb*

```
class User < ApplicationRecord
  has_secure_password
  validates  :username, :email, uniqueness: true, presence: true

  has_many :post
end 
```

现在，我对 post 控制器做了一些修改。主要表现在新建和创建方法上。

```
def new
        @post = current_user.post.build

    end

    def create
        post = current_user.post.build(post_params)
        if post.save
            redirect_to post_path(post)           
        else
            flash[:error] =   "could not save"
            redirect_to new_post_path          
        end
    end 
```

新方法现在所做的是通过获取 current_user 来构建文章并创建文章。

完成后，现在我在每个帖子中都有了一个 post_id。这意味着无论是哪个用户写了这篇文章(或者当时登录了会话),都会被认为是这篇文章的作者。

有了这些，我修改了文件

```
<%  if session[:user_id] == post[:user_id]%>
            <td><%= link_to 'Edit', edit_post_path(post) %></td>
             <td><%= link_to 'Delete', destroy_post_path(post),
              method: :delete,
              data: { confirm: 'Are you sure?' }%></td>
            <% end %> 
```

瞧啊。成功了。条件是只有这时用户才能够破坏或编辑文档。

我打算在做了一些布局更改后，明天在 heroku 上部署应用程序。非常感谢你

[![justalever image](img/cddc8f9a62ae47d446e12e2ccfca5b3b.png)](/justalever)

## [安迪·莱文思](/justalever)

[Product Designer @memberful/@patreon, Blogger/Vlogger @webcrunchblog, Guitarist 🎸 and YouTuber. I enjoy whiskey, metal, and people watching. Most recent launch: Hello Rails (https://hellorails.io)](/justalever)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)just alever](https://twitter.com/justalever)[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)just alever](https://github.com/justalever)[![external link icon](img/7ad9ad23055d49c106b927d92662ca16.png)https://hello ails . io](https://hellorails.io)

帮我度过难关。

另外，这里有一个[回购](https://github.com/Saralkarki/threeirblog)的链接。欢迎任何形式的评论、批评和反馈。:)