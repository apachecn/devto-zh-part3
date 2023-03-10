# 这种类型的第一期

> 原文：<https://dev.to/saral/first-issue-of-this-kind-273l>

这是不可避免的，我的应用程序似乎在我的本地主机上运行良好，但当部署到 heroku 时，它就崩溃了。经过几个小时的努力，我写了这篇文章。我已经部分修复了它，但是，另一个问题仍然存在。明天我会带着新的想法去看一看。这是漫长的一天，有时令人沮丧，但当我回顾这一天时，这是非常值得的努力。

```
2019-02-06T15:45:57.801026+00:00 heroku[router]: at=info method=GET path="/sessions/1" host=threeirblog.herokuapp.com request_id=32d62fd7-3d03-4022-9e89-9333e485c08a fwd="202.51.93.108" dyno=web.1 connect=0ms service=12ms status=200 bytes=2693 protocol=https
2019-02-06T15:46:03.175402+00:00 heroku[router]: at=info method=GET path="/posts/new" host=threeirblog.herokuapp.com request_id=bf9f0490-5bb3-4436-baef-3f3c7c998249 fwd="202.51.93.108" dyno=web.1 connect=0ms service=7ms status=500 bytes=1827 protocol=https
2019-02-06T15:46:03.168691+00:00 app[web.1]: I, [2019-02-06T15:46:03.168598 #4]  INFO -- : [bf9f0490-5bb3-4436-baef-3f3c7c998249] Started GET "/posts/new" for 202.51.93.108 at 2019-02-06 15:46:03 +0000
2019-02-06T15:46:03.169363+00:00 app[web.1]: I, [2019-02-06T15:46:03.169307 #4]  INFO -- : [bf9f0490-5bb3-4436-baef-3f3c7c998249] Processing by PostsController#new as HTML
2019-02-06T15:46:03.172576+00:00 app[web.1]: D, [2019-02-06T15:46:03.172510 #4] DEBUG -- : [bf9f0490-5bb3-4436-baef-3f3c7c998249]   [1m[36mUser Load (1.2ms)[0m  [1m[34mSELECT  "users".* FROM "users" WHERE "users"."id" = $1 LIMIT $2[0m  [["id", 1], ["LIMIT", 1]]
2019-02-06T15:46:03.173490+00:00 app[web.1]: I, [2019-02-06T15:46:03.173429 #4]  INFO -- : [bf9f0490-5bb3-4436-baef-3f3c7c998249] Completed 500 Internal Server Error in 4ms (ActiveRecord: 1.2ms)
2019-02-06T15:46:03.173902+00:00 app[web.1]: F, [2019-02-06T15:46:03.173845 #4] FATAL -- : [bf9f0490-5bb3-4436-baef-3f3c7c998249]   
2019-02-06T15:46:03.173976+00:00 app[web.1]: F, [2019-02-06T15:46:03.173903 #4] FATAL -- : [bf9f0490-5bb3-4436-baef-3f3c7c998249] ActiveModel::UnknownAttributeError (unknown attribute 'user_id' for Post.):
2019-02-06T15:46:03.174011+00:00 app[web.1]: F, [2019-02-06T15:46:03.173967 #4] FATAL -- : [bf9f0490-5bb3-4436-baef-3f3c7c998249]   
2019-02-06T15:46:03.174054+00:00 app[web.1]: F, [2019-02-06T15:46:03.174015 #4] FATAL -- : [bf9f0490-5bb3-4436-baef-3f3c7c998249] app/controllers/posts_controller.rb:22:in `new' 
```

在我的 heroku 控制台日志上看到的错误。关于我应该寻找什么有什么建议吗？我可以看到 Post 的一个致命的未知属性“user_id”。但是，这些代码在我本地主机上运行良好。这是我的 Post 数据库没有被迁移的情况吗？我已经跑了`db:migrate`多次，我真的不知道该如何着手了。关于如何更好地调试并找出是什么行导致了问题，有什么建议吗？

我的`posts_controller.rb`长什么样？我可以从控制台看到，它将我指向第 22 行中的新操作。

```
lass PostsController < ApplicationController
    skip_before_action :require_valid_user!, only: [:index]

    def index
        @post = Post.all
        if session[:user_id] 
            @user_id = current_user.post.all
        end

    end

    def show
        @post = Post.find(params[:id])
    end

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

    def edit
        @post = Post.find(params[:id])
    end

    def update
        @post = Post.find(params[:id])
        if @post.update(post_params)
            redirect_to @post
          else
            render 'edit'
        end
    end

    def destroy
        @post = Post.find(params[:id])
        @post.destroy
            redirect_to root_path
    end

    private 
        def post_params
            params.require(:post).permit( :title , :blob)
        end

end 
```

在此之前，我有一个用户模型的问题，我通过删除用户模型并再次运行`db:migrate`解决了这个问题。这似乎对用户来说很有用，我可以注册和登录了。然而，现在问题似乎出在 post 模型上。

不管怎样，我该考虑一下了，明天再做。

[应用程序](https://threeirblog.herokuapp.com/)