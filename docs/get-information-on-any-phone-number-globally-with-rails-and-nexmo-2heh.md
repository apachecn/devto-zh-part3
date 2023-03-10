# 使用 Rails 和 Nexmo 获取全球任何电话号码的信息

> 原文：<https://dev.to/vonagedev/get-information-on-any-phone-number-globally-with-rails-and-nexmo-2heh>

[![](img/26f0a07f4dfb943a71481c2b34be9f41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rFOOoCYY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/03/NI-w-Rails-1200x600.png)

Nexmo [Number Insight API](https://developer.nexmo.com/number-insight/overview) 让您能够快速实时检查全球任何电话号码的有效性、可达性、漫游状态等。

您可以向 Number Insight API 提出三种类型的请求:

*   **Basic:** 返回数字、国家代码、国家名称和国家前缀的本地和国际格式。
*   **Standard:** 返回所有与基本相同的信息，以及当前和原始的蜂窝运营商、号码是否被移植以及是否正在漫游。
*   **Advanced:** 返回与标准相同的所有内容，以及该号码是否可达以及它是否是有效号码。

此外，您还可以异步发出高级请求。为了进行异步高级请求，您需要提供一个回调 URL。

我们将创建一个 Rails 应用程序，该应用程序可以利用 Number Insight API 在任何电话号码上查找基本、标准和高级号码信息。除了跟随这篇博文，您还可以从 GitHub 克隆一个[工作副本](https://github.com/Nexmo/nexmo-rails-number-insights)。我们将在另一篇博文中探讨如何使用高级异步数字洞察 API。

## 先决条件

在开始之前，我们需要确保我们已经做好以下准备:

*   [轨道 5.2.2+](http://rubyonrails.org/)
*   [红宝石 2.5.3+](https://www.ruby-lang.org/en/downloads/)
*   一个 Nexmo 账户。注册是免费的，你将获得免费积分，立即开始探索。

## 设置铁轨

一旦你创建了你的 Nexmo 账户并且在你的系统上安装了 Ruby on Rails，我们就可以开始了。我们需要做的第一件事是初始化一个新的 Rails 项目。您可以在终端中执行以下命令:

```
$ rails new number-insights-app --database=postgresql 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的命令，我们创建了一个名为`number-insights-app`的新 Rails 应用程序，并指定 PostgreSQL 作为我们的数据库选择。您可以将应用程序的名称更改为您选择的名称，如果您想使用 PostgreSQL 之外的数据库，也可以将数据库参数替换为您首选的数据库。

完成上述命令后，进入新应用程序的目录，在首选的代码编辑器中打开项目。将以下内容添加到`Gemfile` :

```
# Gemfile

gem 'nexmo'
gem 'dotenv-rails' 
```

Enter fullscreen mode Exit fullscreen mode

一旦保存了`Gemfile`，继续从命令行运行`bundle install`。这将把 [Nexmo Ruby gem](https://github.com/Nexmo/nexmo-ruby) 和 [dotenv gem](https://github.com/bkeepers/dotenv) 安装到您的应用程序中。Nexmo gem 提供了对 Nexmo APIs 套件的简单访问，dotenv gem 允许您安全地存储环境变量，而不会在版本控制中暴露它们。

此时，还可以运行`rake db:migrate`。这将为您的应用程序设置数据库模式。出于本演练的目的，我们不打算持久存储收到的数据，但欢迎您这样做。

我们的下一步是创建我们的控制器方法和路线。

### 定义我们的控制器动作

我们正在创建的应用程序将向用户呈现一个包含两个字段的表单:

*   电话号码的文本输入
*   单选按钮，用于选择对哪种数字洞察运行数字

因此，我们将需要以下控制器动作:`index`、`show`和`create`。`index`动作将是我们的应用程序的顶层页面，它将具有 HTML 表单。在发出 API 请求后,`show`动作将显示数据。`create`动作将数据提交给 Nexmo Number Insight API。此外，我们将创建两个私有方法:`nexmo`和`insight_type`。前者将实例化 Nexmo 客户端的一个实例，后者将是我们处理 HTML 表单并决定发出哪种 Number Insight 请求的地方。

#### `index`和`show`动作

`index`和`show`动作是我们最容易定义的。我们只需要定义它们。首先，在`app/controllers/`中创建一个名为`number_insights_controller.rb`的文件，并添加以下内容:

```
# number_insights_controller.rb

class NumberInsightsController < ApplicationController
    def index
    end

    def show
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

#### `create`行动

`create`动作将接收来自用户的表单数据，并将其发送给我们的`insight_type`方法进行处理。因此，我们将动作定义如下，确保我们调用了`#insight_type`方法，将表单参数传递给该方法，并呈现了`show`视图:

```
# number_insights_controller.rb

def create
    insight_type(params)
    render :show
end 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们需要创建前面提到的私有方法来实例化我们的 Nexmo 客户机，处理表单数据并发出正确的 API 请求。在将这些指定为`private`方法之后，让我们首先创建我们的 Nexmo 实例化方法:

```
# number_insights_controller.rb

private

def nexmo
    client = Nexmo::Client.new(api_key: ENV['NEXMO_API_KEY'], api_secret: ENV['NEXMO_API_SECRET'])
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们创建一个方法来处理 HTML 表单数据，并使用适当的 Nexmo API 请求发送它:

```
# number_insights_controller.rb

def insight_type(params)
    if params[:type] == 'basic'
        @data = nexmo.number_insight.basic(number: params[:number])
    elsif params[:type] == 'standard'
        @data = nexmo.number_insight.standard(number: params[:number])
    elsif params[:type] == 'advanced'
        @data = nexmo.number_insight.advanced(number: params[:number])
    else
        flash[:failure] = "Please try to submit the form again, something went wrong."
        redirect_to '/number_insights'
    end
    @data = @data.to_h
end 
```

Enter fullscreen mode Exit fullscreen mode

如上所示，`#insight_type`方法接受一个参数，即 HTML 表单`params`，然后运行一个 if/else 语句来检查所选请求的`type`。如果`type`与已定义的类型之一(例如`basic`、`standard`或`advanced`)不匹配，则与用户共享一条消息，告知出现了错误，用户将被重定向到`index`视图。最后。从 API 请求返回的数据被转换成一个`key:value`散列形式，以便在视图中更容易处理。

此时，我们需要为我们的应用程序定义路由。

### 创建我们的路线

我们的应用程序需要三条路线:

*   到`index`动作的`GET`路线
*   到`show`动作的`GET`路线
*   到`create`动作的`POST`路线

让我们将它们添加到`config/routes.rb`文件:

```
# routes.rb

get '/number_insights', to: 'number_insights#index'

get '/number_insights/show', to: 'number_insights#show'

post '/number_insights/new', to: 'number_insights#create' 
```

Enter fullscreen mode Exit fullscreen mode

为了让我们的应用程序与 Nexmo Number Insight API 通信，我们需要为它提供适当的凭证。我们接下来会这么做。

### 您的 Nexmo API 凭证

要获取您的 Nexmo API 密钥和 API 密码，请导航至 [Nexmo 仪表板](https://dashboard.nexmo.com)，登录后，您将在页面顶部附近看到您的凭证。你所需要做的就是点击眼睛图标来揭示 API 的秘密。您也可以单击每个项目的复制图标，将它们自动复制到剪贴板。

[![](img/fb6facd78d7ada7f2f9ae36291a22be6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VfxjY5Cb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2019/03/api_credentials.png)

您将想要利用您之前安装的`dotenv` gem 的功能，所以在您的项目的根文件夹中创建一个名为`.env`的文件。这是我们存储 Nexmo API 凭证的地方。另外，如果您还没有将`.env`添加到`.gitignore`文件中，这是一个好主意，这样可以确保它不会提交到您的版本控制历史中。

在您的代码编辑器中，打开`.env`文件，并以如下格式添加 API 密钥和秘密，确保将您的凭证放在每行的`=`符号之后:

```
# .env

NEXMO_API_KEY=
NEXMO_API_SECRET= 
```

Enter fullscreen mode Exit fullscreen mode

此时，剩下唯一要做的事情就是创建我们的视图，然后我们的应用程序就可以使用了。

### 建立我们的观点

我们需要两个视图，我们还需要在应用程序布局中为我们之前定义的`flash`错误消息添加一个空间，以防用户在 HTML 表单中提供无效的`type`输入。让我们先为错误消息添加空间。

打开`app/views/layouts/application.html.erb`文件，在新的一行中，在开始的`<body>`标签之后和`<%= yield %>`标签之前添加以下内容:

```
# application.html.erb

<% if flash %>
    <% flash.each do |key, msg| %>
        <%= msg %>
    <% end %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果在处理表单数据的过程中出现错误消息，用户将会在页面顶部看到它。

#### `index`查看

`index`视图包含 HTML 表单，用户在该表单中提交他们正在寻找的电话号码和他们正在请求的洞察类型。

在`/views/`中创建一个名为`number_insights`的新文件夹，并在其中添加一个`index.html.erb`文件。在`index.html.erb`文件中，让我们创建我们的表单:

```
# index.html.erb

<h2>Nexmo Number Insights with Rails</h2>

<%= form_tag('/number_insights/new') do %>
    <%= label_tag(:number, "Phone Number:") %>
    <%= text_field_tag(:number) %>
    <br /><br />
    <%= radio_button_tag(:type, "basic") %>
    <%= label_tag(:type_basic, "Basic") %>
    <br />
    <%= radio_button_tag(:type, "standard") %>
    <%= label_tag(:type_standard, "Standard") %>
    <br />
    <%= radio_button_tag(:type, "advanced") %>
    <%= label_tag(:type_advanced, "Advanced") %>
    <br /><br />
    <%= submit_tag("Search") %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

我们利用 Rails 视图助手创建一个 HTML 表单，向`/number_insights/new`提交一个`POST`请求，包含一个文本字段、三个单选按钮和一个提交按钮。

#### `show`查看

在`show`视图中，我们正在创建一个 HTML 表，它将使用从我们的 API 请求返回的数据动态填充其标题行和数据行。通过这种方式，我们不需要为每种类型的 API 请求硬编码一个表，而是让应用程序为我们完成这项工作。如果您还记得，我们将从 API 请求返回的数据转换成了一种`key:value`散列格式，并将其保存到了`@data`实例变量中。我们将使用`keys`作为标题行，使用`values`作为数据行。

需要注意的一个重要事项是，`standard`和`advanced` API 请求中返回的一些数据项包含散列，因此我们需要对这些嵌套的散列进行第二级迭代，以检索其中的数据。这就是从下面的`<% if value.class == Nexmo::Entity %>`条件检查开始所发生的事情。

```
# show.html.erb

<h2>Number Insight Details</h2>
<h3>Number: <%= params[:number] %></h3>

<table>
    <tr>
        <% @data.each do |key, value| %>
            <th><%= key %></th>
        <% end %>    
    </tr>
        <tr>
        <% @data.each do |key, value| %>
            <% if value.class == Nexmo::Entity %>
                <td>
                    <% value.to_h.each do |subkey, subvalue| %>
                        <%= subvalue %>
                    <% end %>
                </td>
            <% else %>
                <td><%= value %></td>
            <% end %>
        <% end %>
    </tr>
</table> 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经完成了应用程序的创建。继续尝试使用不同的电话号码和不同的 Nexmo Number Insight API 请求类型。您可以从命令行使用`rails -s`启动您的应用程序，并在 web 浏览器中导航到`http://localhost:3000/number_insights`。恭喜你！

### 进一步阅读

如果您有兴趣了解有关 Nexmo Number Insight API 的更多信息，请查看以下内容:

*   [Nexmo Number Insight API 参考](https://developer.nexmo.com/api/number-insight)
*   [利用新的 Nexmo 号码洞察功能获取完整的来电者数据](https://www.nexmo.com/blog/2017/03/21/know-caller-name-cnam-number-insight/)
*   [使用 Ruby 和 Number Insight API 验证数字](https://developer.nexmo.com/tutorials/validate-a-number)