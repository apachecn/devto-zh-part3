# Ruby on Rails 中的嵌套注释

> 原文：<https://dev.to/lucysuddenly/nested-comments-in-ruby-on-rails-1k1f>

[![Nesting Dolls](img/8175024018ea4055698732da3e728646.png)](https://i.giphy.com/media/11wjBYmS9Q9jTa/giphy.gif)

对于我最近的项目来说，嵌套注释是一个非常理想的可交付成果，所以我学会了如何实现它！以下是必要的步骤，让你也能做到:

## 第一步:多态关联

将你的模型设置成多态的。

[![Polymorphic associations](img/2be8a93a31782778bb4ff29293d734df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hD4pdR95--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/dy1glok.png)

<small>[摘自《Ruby on Rails 指南》](https://guides.rubyonrails.org/association_basics.html#polymorphic-associations)</small>

因此，在我们的例子中，我们希望让评论具有多态性，因为它们可以在帖子或其他评论上发表。我们这样设置迁移:

```
rails generate model comment content:string commentable_id:integer commentable_type:string 
```

保存评论时，“帖子”或“评论”将被保存到 commentable _ type，帖子或评论的主键将被保存到 commentable _ id。这就像是“属于/拥有”关系的一个开关。

帖子和评论的模型应该分别是这样的:

```
class Post < ActiveRecord::Base
    has_many :comments, as: :commentable
end

class Comment < ActiveRecord::Base
    belongs_to :commentable, polymorphic: true
    has_many :comments, as: :commentable
end 
```

## 第二步:路由

[![routing monorails](img/4b65bfd82a14452c0cc61c651b2454cd.png)](https://i.giphy.com/media/dlz26Lw2yfdg4/giphy.gif)

您的 routes.rb 应该是这样的，因为我们希望能够为 commentable _ id 传入主键(params[:id])，为 commentable _ type 传入“comment”或“post”——我们从 URI 获得这些。

```
Rails.application.routes.draw do

  resources :posts do
    resources :comments
  end

  resources :comments do
    resources :comments
  end

end 
```

我们的

`rake routes`

会是这个样子:

```
 Prefix Verb   URI Pattern                                       Controller#Action
       post_comments GET    /posts/:post_id/comments(.:format)                comments#index
                     POST   /posts/:post_id/comments(.:format)                comments#create
    new_post_comment GET    /posts/:post_id/comments/new(.:format)            comments#new
   edit_post_comment GET    /posts/:post_id/comments/:id/edit(.:format)       comments#edit
        post_comment GET    /posts/:post_id/comments/:id(.:format)            comments#show
                     PATCH  /posts/:post_id/comments/:id(.:format)            comments#update
                     PUT    /posts/:post_id/comments/:id(.:format)            comments#update
                     DELETE /posts/:post_id/comments/:id(.:format)            comments#destroy
               posts GET    /posts(.:format)                                  posts#index
                     POST   /posts(.:format)                                  posts#create
            new_post GET    /posts/new(.:format)                              posts#new
           edit_post GET    /posts/:id/edit(.:format)                         posts#edit
                post GET    /posts/:id(.:format)                              posts#show
                     PATCH  /posts/:id(.:format)                              posts#update
                     PUT    /posts/:id(.:format)                              posts#update
                     DELETE /posts/:id(.:format)                              posts#destroy
    comment_comments GET    /comments/:comment_id/comments(.:format)          comments#index
                     POST   /comments/:comment_id/comments(.:format)          comments#create
 new_comment_comment GET    /comments/:comment_id/comments/new(.:format)      comments#new
edit_comment_comment GET    /comments/:comment_id/comments/:id/edit(.:format) comments#edit
     comment_comment GET    /comments/:comment_id/comments/:id(.:format)      comments#show
                     PATCH  /comments/:comment_id/comments/:id(.:format)      comments#update
                     PUT    /comments/:comment_id/comments/:id(.:format)      comments#update
                     DELETE /comments/:comment_id/comments/:id(.:format)      comments#destroy
            comments GET    /comments(.:format)                               comments#index
                     POST   /comments(.:format)                               comments#create
         new_comment GET    /comments/new(.:format)                           comments#new
        edit_comment GET    /comments/:id/edit(.:format)                      comments#edit
             comment GET    /comments/:id(.:format)                           comments#show
                     PATCH  /comments/:id(.:format)                           comments#update
                     PUT    /comments/:id(.:format)                           comments#update
                     DELETE /comments/:id(.:format)                           comments#destroy 
```

你会注意到我们有三组 comment CRUD 动作:一组用于 post comments，一组用于 comment comments，一组用于 comments——但是它们都共享一组控制器动作，所以一切都是正常的。在控制器中，我们将区分哪个 commentable 将被传递到 build 方法中。

## 第三步:控制器

[![video game controller](img/d97fc86ca922cc198bdced70e21f484a.png)](https://i.giphy.com/media/901ojwtVHZAze/giphy.gif)

我们的注释控制器应该是这样的:

```
class CommentsController < ApplicationController
before_action :find_commentable, only: :create

    def new
      @comment = Comment.new
    end

    def create
      @commentable.comments.build(comment_params)
      @commentable.save
    end

    private

    def comment_params
      params.require(:comment).permit(:content)
    end

    def find_commentable
      if params[:comment_id]
        @commentable = Comment.find_by_id(params[:comment_id]) 
      elsif params[:post_id]
        @commentable = Post.find_by_id(params[:post_id])
      end
    end

end 
```

你抓住它了吗？这就是奇迹发生的地方。如果来自 URI 的参数包含一个 comment_id，我们传入的 commentable 将是一个注释；如果参数包含 post_id，我们传入的 commentable 将是一个 post！

## 第四步:视图

[![great view](img/1f470d2b18fef92a6073a08e49865561.png)](https://i.giphy.com/media/3o6nVam54YqA3vlxzq/giphy.gif)

在我们的 post show 视图的末尾，我们应该包含下面对我们将要创建的分部的引用:

```
<ul>
<%= render partial: 'comments/comment', collection: @post.comments %>
</ul> 
```

以下是 collection 如何使用 render 方法:

[![rendering](img/88addd0b3a81274803ea268e5cd60844.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SqRFIZta--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/888J7ZV.png)

<small>[摘自《Ruby on Rails 指南》](https://guides.rubyonrails.org/layouts_and_rendering.html#rendering-collections)</small>

现在我们可以访问每个单独的评论——这几乎就像是在一个带有|comment| delared 的 each 块中。下面是我们的部分视图“_comment”可能的样子:

```
<li>
  <%= comment.content %> -

  <%= form_for [comment, Comment.new] do |f| %>
      <%= f.text_area :content, placeholder: "Add a Reply" %><br/>
      <%= f.submit "Reply"  %>
      <% end %>

  <ul>
      <%= render partial: 'comments/comment', collection: comment.comments %>
  </ul>

</li> 
```

所以我们显示注释的内容，包括一个新的嵌套注释的 form_for，然后再次呈现我们的部分内容！递归非常有用！但是请注意我们的 post 呈现方法和 comment partial 呈现方法之间的区别:前者的集合呈现文章的评论，而每个评论将呈现它的任何*评论。很漂亮，对吧？*

下面是嵌套注释的大概样子:

[![nested comments](img/c065a2160a94c036ca44b6c400bd523a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9h2xKtIc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/H9zbb8h.png)

往前走！嵌套你的评论！