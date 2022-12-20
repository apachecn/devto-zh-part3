# Rails 路由和 MVC 布局课程。

> 原文：<https://dev.to/ackers93/lessons-on-rails-routing-and-mvc-layouts-4c9j>

自今年 3 月以来，我一直在学习 Rails，我在业余时间在 Treehouse 学习 Ruby 大约 2 年了，所以决定是时候学习 Rails 如何工作并开始理解它的所有“魔力”了，希望这是记录我的学习曲线的几篇博客文章中的第一篇，并给我(和其他人)一个资源，当我忘记事情如何工作时，我可以回来。

Rails 的一个基本部分是 MVC 布局，它代表模型、视图和控制器，这是 Rails 应用程序的三个主要元素。

首先，让我们创建一个简单的应用程序，我更喜欢使用 Postgres，因为我在默认 Sqlite3 的 Gemfile 依赖性方面遇到了麻烦(这是我稍后要学习处理的事情)，所以我将在我的终端命令中指定它。

`rails new demo --database=postgresql`

这将在当前目录中创建名为“demo”的应用程序，接下来要做的是使用终端转移到该应用程序文件夹。创建 MVC 布局的一般规则是按照字母的顺序，从你的模型开始，移动到你的视图，最后是你的控制器，但是这并不是一个硬性的规则，只是一个惯例，为了这篇文章的方便，我将打破这个规则去 MCV，但是有很多 stackoverflow 的文章来说明为什么它更常用于 MVC。因此，让我们从创建模型开始

> 模型定义了某物是什么，以及它有什么能力。

我将创建一个简单的食物清单，也许是为了知道我们的橱柜里还剩多少食物。

`rails generate model Food name:string size:integer`

这将创建以下迁移文件。

```
class CreateFoods < ActiveRecord::Migration[5.2]
  def change
    create_table :foods do |t|
      t.string :name
      t.string :size

      t.timestamps
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

对于我们的简单应用程序来说，这似乎很好，所以运行`rake db:migrate`来运行迁移。

现在我们已经创建了模型，让我们继续生成控制器。

> 控制器处理所有复杂的逻辑，渲染局部，保存东西到数据库，并给用户反馈。

`rails generate controller foods`

请注意，控制器被标记为模型的复数，并且没有大写字母。如果做得不对，他们可能很难互相联系。

这将生成以下内容。

```
class FoodsController < ApplicationController
end 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，它并没有真正做什么，所以我们需要添加一些方法来修改和添加到我们的应用程序中，就像这样...

```
class FoodsController < ApplicationController
  def index
  end

  def show
  end

  def new
  end

  def create
  end

  def edit
  end

  def update 
  end

  def destroy
  end

  private
   def allowed_params
       params.require(:food).permit(:name, :size)
   end

end 
```

Enter fullscreen mode Exit fullscreen mode

这些是我们在一个典型的应用程序中需要的所有方法，虽然它们现在是空的，我们稍后会回来添加它们。同时，我们需要将我们的模型链接到我们的控制器，我们通过进入我们的`/config/routes.rb`文件来完成。并将 Rails 助手方法`resources :foods`添加到我们的列表中。这将为我们的食品管理员创造必要的路线。这也可以写成长的形式，详细说明每条路线。

```
 get "/foods", to: "foods#index"
  get "/foods/:id", to: "foods#show"
  get "/foods/new", to: "foods#new"
  post "/foods", to: "foods#create"  # usually a submitted form
  get "/foods/:id/edit", to: "foods#edit"
  put "/foods/:id", to: "foods#update" # usually a submitted form
  delete "/foods/:id", to: "foods#destroy" 
```

Enter fullscreen mode Exit fullscreen mode

这是解释路由的好时机。虽然一般来说你只有`resources :id`可以看到你的路线列表。回到你的终端，输入`rake routes`。你可以看到上面的列表..我发现我花了一段时间来适应阅读这种格式的路线，但一旦你花了一些时间，它真的很有用！

浏览器中的一个 URL 向我们的路由器(config/routes.rb)发送一个请求，路由器将分析该 URL，找到并发送到匹配控制器中的匹配操作，匹配控制器将尝试与视图匹配。

现在我们已经看到了模型和控制器是如何工作的，并且控制器正在搜索一个视图，让我们创建所有必要的视图。

> 视图是一个模板，是显示给用户的东西..最常见的是 HTML 文档。

在你的编辑器中找到你的“app/views/foods”文件夹，创建文件“index.html.erb”，然后点击“show”、“edit”和“new”。

首先，让我们创建我们的“新”视图。首先，我们需要编辑我们的 FoodsController，添加到我们的新方法中。

```
def new
    @food = Food.new
  end 
```

Enter fullscreen mode Exit fullscreen mode

这创建了我们的食物模型的一个新实例，并将其分配给`@food`实例变量。现在我们转到“new.html.erb”并创建我们的页面。我们只是想要一个简单的形式来添加食物的名称和大小到我们的数据库中..

```
<%= form_for @food do |f| %>

  <%= f.label :name  %> <br>
  <%= f.text_field :name %> <br>

  <%= f.label :size %> <br>
  <%= f.text_field :size %> <br>

  <%= f.submit 'Save Food Item' %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候启动你的服务器了，去[http://localhost:3000/foods/new](http://localhost:3000/foods/new)看看吧。您的表单将在那里，您将能够添加到您的数据库，但没有定义其他路线。您无法查看、编辑或删除该数据，并且尝试保存信息会为“foods/create”抛出“Template is missing”错误，因此让我们添加该操作。

```
 def create
    @food = Food.new(allowed_params)

    if @food.save
      redirect_to foods_path
    else
      render 'new'
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

因此，这个方法以我们输入到表单中的食物模型为例，并尝试保存它，如果成功，我们将被重定向到我们的索引方法(通过`foods_path`)，否则，它将引导我们回到我们的新表单。
因为您将被转到我们的索引页面，如果我们现在创建一个，将会很有帮助。首先，添加到您的索引方法。

```
 def index
    @foods = Food.all
  end 
```

Enter fullscreen mode Exit fullscreen mode

这将从数据库中取出所有的食物，并将它们列在我们的索引视图页面上，我们需要编辑所有的来完成这项工作。将以下内容添加到您的视图中。

```
<% @foods.each do |food| %>
  <p>
    Name: <%= food.name %><br>
    Size: <%= food.size %> | <%= link_to 'Edit', edit_food_path(food) %>
  </p>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

这将遍历您的每个数据库项，然后放在单独的段落中。在浏览器中尝试这些新功能，看看是否会遇到问题，这是一个好主意。如您所见，为了节省空间，我还在前面的代码示例中开始了编辑功能。是时候为控制器中的编辑按钮赋予功能了，因为我们需要在使用物品时更新库存。

```
 def edit
    @food = Food.find(params[:id])
  end 
```

Enter fullscreen mode Exit fullscreen mode

这使得编辑功能能够在数据库中搜索由`(params[:id])`标识的要编辑的正确项目

对于您的视图，只需从您的“new.html.erb”复制并粘贴到您的“edit.html.erb”。这将提供编辑的表单(带有概要字段),但如果您尝试一下，您会发现它不会保存更新的值，这表明我们的错误是我们的 FoodsController 的更新功能没有模板，所以现在是时候添加它了。

```
 def update
    @food = Food.find(params[:id])
    if @food.update_attributes(allowed_params)
      redirect_to foods_path
    else
      render 'new'
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

这与我们的编辑和创建功能的工作方式类似。找到项目，更新它的属性，并根据它是否可以被保存，重定向我们到我们需要的地方。

在我们的索引页面上有两个有用的途径，一个是添加条目，另一个是删除条目。像这样编辑 index.html.erb 文件将会添加这些链接。

```
<%= link_to 'Add Food', new_food_path %>

<% @foods.each do |food| %>
  <p>
    Name: <%= food.name %><br>
    Size: <%= food.size %> | <%= link_to 'Edit', edit_food_path(food) %> |
    <%= link_to 'Delete Item', food, method: :delete %>
  </p>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

“添加食物”链接将自动工作，但是需要编辑 FoodsController 中的销毁方法。应该是..

```
 def destroy
    @food = Food.find(params[:id])
    @food.destroy
    redirect_to foods_path
  end 
```

Enter fullscreen mode Exit fullscreen mode

添加之后，继续删除您的一个记录，看到它删除了它，并立即将我们重定向回我们的`foods_path`，这是我们的索引页面。

我们需要添加的最后一个方法是我们的 Show 方法。我们将使用这个来允许我们单独查看每个项目。在 index.html.erb 中显示我们的 name 属性的地方，按以下方式编辑它。

```
 Name: <%= link_to food.name, food_path(food) %><br> 
```

Enter fullscreen mode Exit fullscreen mode

这将导致我们的显示方法(你可以看到，如果你在终端再次使用你的`rake routes`命令),现在我们需要给它一个视图显示在“show . html . erb”

```
<h1><%= @food.name %></h1> 
```

Enter fullscreen mode Exit fullscreen mode

这将显示一个变量的名字作为一个标题，这还没有被定义。我们需要做的就是编辑控制器的 Show 函数来搜索我们的项目，并将其分配给视图想要显示的变量。

```
 def show
    @food = Food.find(params[:id])
  end 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成。我们已经考虑了所有的路线，现在我们有了这个基本的设置，这只是一个复制它的想法来完成更复杂任务的例子。

我希望这对你和我写这篇文章一样有帮助！我将感谢任何反馈，纠正和问题，因为我很高兴继续学习和学习教授这些概念。