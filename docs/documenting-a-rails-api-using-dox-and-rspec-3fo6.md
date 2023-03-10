# 使用 Dox 和 RSpec 记录 rails API

> 原文：<https://dev.to/jean182/documenting-a-rails-api-using-dox-and-rspec-3fo6>

嗨，在这篇文章中，我想谈谈一个名为 [dox](https://github.com/infinum/dox) 的宝石，在大学期间，我正在创建一个将被 android 应用程序使用的 rails 后端，这个项目要求我记录 API，所有的端点和 API 为了工作而发送和接收的东西。

我以前从未做过类似的事情，但在我做研究时，我想到的第一件事是，rails 当然会有一个可以为 API 端点生成现成文档的 gem，在挖掘了几个小时后，我发现其他 gem 也做了同样的事情，但它们不符合我的要求，或者它们的用法不是我愿意使用的， 例如，在控制器上引用它们，或者在 spec 目录中创建一个新的子文件夹，其中包含专门针对 gem 的新测试。

Dox 是我从其他选项中选择的一个，因为它为您生成所有基于用 RSpec 生成的测试的文档；即使它需要在您的 spec 文件夹下创建新的文件，它也可以很容易地与您现有的测试集成。不过这并不神奇，因为你需要配置它，而且没有太多关于设置 gem 的文档，只有基础知识，所以在这篇文章中，我将讨论如何在 rails API 上集成 gem。

首先我们需要设置项目:

```
$ rails new book-app --api -T --database=postgresql 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们需要将 RSpec 和 dox 集成到我们的 Gemfile 中:

```
group :development, :test do
# Rest of the gems on this block.....
  gem 'rspec-rails', '~> 3.8'
end
group :test do
  gem "dox", require: false
end 
```

Enter fullscreen mode Exit fullscreen mode

然后运行以下命令来安装它:

```
$ bundle install

$ rails generate rspec:install 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在阅读这篇文章，你应该熟悉 RSpec 的安装，我不会做很多 RSpec 相关的配置，只是必要的步骤，将 dox 与你的应用程序集成。

在 rails helper 文件中，您需要在 rails_helper 中配置 dox，以便与 RSpec 集成。

```
# spec/rails_helper.rb
require "dox" # This should go below the RSpec require

# This is telling RSpec to use all of the files in that folder you can name it as you want, in this case it will be "docs"

Dir[Rails.root.join("spec/docs/**/*.rb")].each { |f| require f }
RSpec.configure do |config|
  config.after(:each, :dox) do |example|
    example.metadata[:request] = request
    example.metadata[:response] = response
  end
end
# This last config is to specify which files are the ones which are going to be in charge to be associated with the created resource
Dox.configure do |config|
  config.header_file_path = Rails.root.join("spec/docs/descriptions/header.md")
  config.desc_folder_path = Rails.root.join("spec/docs/descriptions")
  config.headers_whitelist = ["Accept"]
end 
```

Enter fullscreen mode Exit fullscreen mode

这差不多是我们需要的所有配置，所以让我们生成一个支架并运行迁移，以记录一些东西:

```
$ rails g scaffold Book title plot:text

$ rails db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果一切正常，您应该看到支架生成的测试，在这种情况下，我们将使用控制器生成的测试，用于学习目的。使用 Dox 的关键是，您需要指定您想要使用控制器规范记录哪个端点，如果您正在搭建，它将生成整个资源。

现在，我们需要在我们的 spec 文件夹中创建一个名为 docs 的文件夹，该文件夹将包含另一个名为 descriptions 的文件夹。md 文件，这些文件将在您的文档下显示静态信息，通常是关于该资源的信息，例如简短的描述。我们将创建我们的 book.md 文件，该文件将与我们刚刚创建的 scaffold 相关联，我们还需要一个 header.md 文件，该文件将包含我们的 API
的标题和描述

```
# spec/docs/descriptions/books.md

### Resource for Book

Here we will have all the endpoints for the book resource

# spec/docs/descriptions/header.md

# Books API

This are the docs for the books API, all the resources and how to use it. 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经有了资源的描述和 API 描述，我们需要在 docs 文件夹中为我们的图书创建一个文件，该文件将包含该资源的所有文档结构。

```
# spec/docs/books.rb
module Docs
  module Books
    extend Dox::DSL::Syntax
    document :api do
      resource "Books" do
        endpoint "/books"
        group "Books"
        desc "books.md" # This will point the description we created
      end
    end
    document :index do
      action "Get books"
    end
    document :show do
      action "Get a book"
    end
    document :update do
      action "Update a book"
    end
    document :create do
      action "Create a book"
    end
    document :destroy do
      action "Delete a book"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的文件结构，如果你想的话，你可以创建你自己的文件结构，这些就像我们的文档中关于资源的部分。

为了使用 dox，我们需要将它添加到我们的控制器中，该控制器负责测试上述动作。我们必须将这个文件添加到控制器规范文件中，就像这样:

```
# spec/controllers/books_controller_spec.rb
RSpec.describe BooksController, type: :controller do
  include Docs::Books::Api # This referencing the document :api in the books.rb file in our docs folder# Index Example using dox
 describe "GET #index" do
  include Docs::Books::Index # This is referencing the action we created under books.rb
    it "returns a success response", :dox do book = Book.create! valid_attributes
      get :index, params: {}, session: valid_session
      expect(response).to be_successful
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们导入了在 spec/docs/books.rb 下创建的索引，这告诉 dox，当我们运行 r spec 中的 describe 时，我们将使用 map 来映射文档块中的所有结果。请注意，我们需要在 it 语句中添加:dox，以便让它工作。

最后，要创建您的文档，您需要运行以下命令:

```
$ bundle exec rspec spec --tag apidoc -f Dox::Formatter --order defined --out public/api/docs/v1/apispec.md 
```

Enter fullscreen mode Exit fullscreen mode

这将在 public/api/docs/v1 下创建一个名为 apispec 的新的 md 文件，并且将有一个包含所有生成的文档的 MD 文件，您可以创建更多的资源，如果您运行该命令，它也将在该文件上生成。

所以拥有一个 md 文件是很好的，但是如何将 md 文件映射到 html 文件呢？当然，我们可以用其他工具做到这一点，dox 支持三种不同的现成渲染器([养蜂场](https://apiary.io/)、[阿格里奥](https://apiary.io/)、[滑雪板](https://github.com/bukalapak/snowboard))，它们负责使用我们刚刚创建的 md 文件创建一个令人敬畏的页面。在这个例子中，我决定使用 aglio，我还没有尝试其他的，但他们似乎也很酷。

对于 aglio，您需要安装 node 和 npm，因此您需要像安装全局包一样安装它，以便在任何地方使用它，就像在我们的 rails 项目中一样。

如果你有一个全局包，你需要运行这个命令。

```
$ aglio --include-path / -i public/api/docs/v1/apispec.md -o public/api/docs/v1/index.html 
```

Enter fullscreen mode Exit fullscreen mode

之后，您将在 md 文件所在的文件夹 public/api/docs/v1/中有一个 index.html 文件，如果您在服务器运行时打开该文件或转到该路径，您应该会看到如下内容:

【 [![Generated Page with Aglio](img/34cec1f1e10d22f49bbbed7a586e01dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qrIc4Vzm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://loserkid.io/static/48970312397c4ee9fc53fd5e35ec641d/75d76/index-page.png)

正如你所看到的，它生成了一个很好的文档页面，非常容易制作，可以很快集成到你现有的项目中，并且它使你的单元测试更加有用，因为现在它们可以帮助你绘制文档。

我用这个[回购](https://github.com/infinum/dox-demo)做指导。

我为本教程生成的 [repo](https://github.com/jeanm182/Book-Example) 。

你可以在这里看到我的大学项目文档使用 DOX(仍在进行中)和更多的资源。

(这是我发布在 loserkid.io 博客上的一篇文章，你可以通过[点击这里](https://loserkid.io/documenting-a-rails-api-using-dox-and-rspec/)在线阅读。)