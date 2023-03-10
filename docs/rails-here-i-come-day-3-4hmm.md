# 铁轨！我来了(第三天)

> 原文：<https://dev.to/saral/rails-here-i-come-day-3-4hmm>

从我昨天停下的地方开始。我手动将数据输入数据库，并提取数据进行查看。今天，我的目标是更上一层楼，在一个表单上输入数据，并获取这些数据以供查看。

我本来可以使用 rails 上可用的`resources`，但是因为我想从头开始创建我的第一个帖子页面，所以我选择手动创建所有的控制器和路径。

### 1。构建一幅心理图像

我首先在脑海中构建了一幅需要什么的画面。我需要一个带有 get 动词(`get`方法)的路由来重定向到 post 页面，我还需要一个 post 动词(`post`方法)来将用户输入数据保存到我的数据库中。我将使用我昨天创建的同一个数据库`Post`。在我的 rails 控制台中，我用一个快速的`Post.delete_all`清除了我的数据库。

接下来，我在 route.rb 中准备好了路由。我需要一个`/post/index`路由来查看数据库中的所有帖子(只有标题)，`posts/new`路由到表单，`posts/:id`路由到显示完整帖子文本的显示页面。我还使用了一个`as`命令将`posts#new`的前缀改为`new_post`，将`posts#show`改为`post`。这些对以后会很有帮助。我还改变了这个练习的主页。我的`posts/index`将是我的主页。我还需要一个`post`方法从表单中获取用户输入。

```
Rails.application.routes.draw do
get 'posts/index'
root 'posts#index'
post 'posts' => 'posts#create'
get 'posts/new'  => 'posts#new' as: 'new_post'
get 'posts/failure' => 'posts#failure` , as: 'failure'
get 'posts/:id' => 'posts#show', as: 'post'

end 
```

#### 提示:记住`posts`的所有方法都要在`posts/id`之前`get`，否则 rails 会开始把`new`、`failure`等动作当作`id`，当找不到这样的 id 时(因为它们不存在)，rails 会抛出一个错误。

### 2。设置控制器

我昨天已经通过`$rails g controller posts`生成了 posts 控制器。因此，我着手对控制器进行更新。
首先，我定义了一个索引函数，根据需要显示数据库中的所有内容。接下来我按照我的路线在`posts`控制器中定义了动作`new`和`show`。最后，我不得不为`create`设置动作。

```
class PostsController < ApplicationController

    def index  
        @posts = Post.all   
    end

    def show
        @post = Post.find(params[:id])
    end

    def new
        @post = Post.new
    end

    def create
        post = Post.new(post_params)
        if post.save
            redirect_to post_path(post)           
        else
            redirect_to new_post_path          
        end
    end

    private 
        def post_params
            params.require(:post).permit(:title,:blob,:author)
        end
end 
```

*   分解每个动作

    *   昨天描述的索引操作从 Post 数据库中获取所有的帖子，并将其存储在实例 [@post](https://dev.to/post) 中，以便以后在视图中使用。
    *   show 动作从数据库中搜索并找到`:id`。此` param[:id]是从路线中生成的。
    *   新动作(这里我只是猜测)从表单中获取数据，并将其存储在实例 [@post](https://dev.to/post) 中。这些数据现在可以保存到数据库中了。它还没有被写入数据库，但是，我们已经从新页面的表单中收到了数据。所以，在我注释了`create`动作的代码后，我在我的控制台上做了一个快速检查。

        ```
        Processing by PostsController#create as HTML
        Parameters: {"utf8"=>"✓",             
          "authenticity_token"=>"z7vZT7ReRs6LKg5bJuGgnMTuEzYwbDbdDvZmi+HAf2CCNICj2CEm/uxPX6Z            WqqEy0GPMLLGLL//Bl+BT/D38kQ==", "post"=>{"title"=>"Introduction",     "blob"=>"Hello World. How are you?", "author"=>"Saral"}, "commit"=>"Submit"}
        No template found for PostsController#create, rendering head     :no_content
        Completed 204 No Content in 56ms (ActiveRecord:          0.0ms) 
        ```

        让我试着分解一下，我可以看到标题、blob(或正文)和作者正在提交。然而，随后它抛出一个错误，说没有为`PostsController#Create`找到模板。这给了我一个提示，我需要在`PostsController#create`中呈现一个模板。

    *   最后，创造活动是神奇的地方。在这里，我声明了一个私有函数，它将 posts 的参数定义为`post_params`(需要更好地理解这一点)。参数使用方法。要求，并说它需要从数据库中的职位，并只允许从表单的标题，blob(主体)和作者输入，并保存到数据库中只有这些。使用它的一个原因是为了安全措施。有了这一点，就可以防止 SQL 注入，恶意用户就不能向数据库发布无用和有害的内容。

        完成后，创建动作获取私有函数中定义的 post_params。然后检查条件，如果帖子保存成功，它将重定向到 post_path。如果您还记得，post_path 是 posts/:id 页面。因此，我们被重定向到一个页面`posts/1`。在这种情况下,“1”是一个伪 id。随着数据库中项目数量的增长，id 将不断变化。最后，如果数据无法写入我们的数据库，我们重定向到一个失败页面。

        一旦所有这些都完成了，我的视图也相应地设置好了，我就可以将数据从表单发送到我的视图页面了。

但是，等等，我们如何设置视图？我们如何在后端和前端之间建立连接？

### 3。风景

*   设置新页面

    ```
    <div class="container">
    <div class = "row">
    <div class="col-md-12 post_heading">
        <h1> Your blog post goes here </h1>
    </div>
    </div>
    <div class="row">
    <div class="col-md-12">
        <%= form_for(@post) do |f|%>
            <div class="form-group">
                <%= f.label :title %><br>
                <%= f.text_field :title , class: "form-control" , placeholder: "Title" , :required => true %>
            </div>
            <div class="form-group">
                <%= f.label :blob, 'Story' %><br>
                <%= f.text_area :blob , class: "form-control blog_body" , rows: 20, :required => true %>                  
            </div>
            <div class="form-group text_size">
                <%= f.label :author %><br>
                <%= f.text_field :author , class: "form-control"%>
            </div>
            <div id = "btn">
                <%= f.submit "Submit", class: "btn btn-primary" %>
            </div>
         <% end %>           
    </div> 
    ```

这里值得注意的是嵌入的 ruby 代码，用于生成常规 html/css 旁边的表单。我们用`form_for`的方法得到变量 [@post](https://dev.to/post) 。回想一下，我们如何在控制器中设置`@post = Post.new`。我们使用这个变量来保存标题、blod(正文)和作者的名字。另外，请注意我是如何对 ruby 代码使用引导类的。最后一页看起来像这样

[![Alt New Post View](img/7b376e62fa5ce5964a11a18e5ea8ee26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UaJu_u0v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.postimg.cc/HnHpWyML/newpost-at-9-38-42-PM.png)

最后，由于使用了与昨天的`index.html`类似的 html 视图，我能够获取和查看数据。

```
 <div class="container">

    <h1> Post List </h1>
    <table class="table">
    <thead class="thead-dark">
    <tr>
        <th scope="col"> Title </th>
    </tr>
    </thead>
    <% @posts.each do |post| %>
    <tr>
        <td> <%= link_to post.title, post_path(post) %> </td>
    <% end %>  
    </table>
    </div> 
```

[![Alt Final Image](img/bc4b04ca4cfa1223f0e585a6b581a3f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--px3jUr3m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.postimg.cc/3wnjFXKs/Screen-Shot-2019-01-30-at-9-43-03-PM.png)

最后一句话，我今天做的事情还有很多要解释，但总的来说，我认为这篇文章总结了它。我可以写更多关于视图和控制器的东西，但是我觉得随着我在编码和编写过程中的前进，我将能够更好地解释这些东西。

明天，我们将更新和删除帖子。此外，如果时间允许，我们看看用户数据库和注册。