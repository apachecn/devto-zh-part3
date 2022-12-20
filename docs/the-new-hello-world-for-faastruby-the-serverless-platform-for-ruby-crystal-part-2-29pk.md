# faastRuby 的新 Hello World——Ruby & Crystal 的无服务器平台——第 2 部分。

> 原文：<https://dev.to/sergiosouzalima/the-new-hello-world-for-faastruby-the-serverless-platform-for-ruby-crystal-part-2-29pk>

[![alt text](img/af1fd29649cee0c1ad3d50cc17c574ee.png "faastRuby logo")](https://res.cloudinary.com/practicaldev/image/fetch/s--2IyWzhJz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://faastruby.io/wp-content/uploads/2019/03/logo-negative-1.png)

嗨伙计们！在我们开始之前，如果你还没有读过我的前一篇文章 faastRuby 的新 Hello World。Ruby & Crystal 的无服务器平台:[第 1 部分](https://dev.to/sergiosouzalima/the-new-hello-world-for-faastruby-the-serverless-platform-for-ruby-crystal-part-1-2o95) -你一定要看一下，因为我们将在那里学习的基础上进行构建。

### 我们要创造什么？

使用 [faastRuby](https://www.faastruby.io) 平台，我们将在 Ruby 中创建一个函数，并使其在互联网上快速可用。

## 逐步创建功能

### 登录

进入我们之前创建的文件夹[第 1 部分](https://dev.to/sergiosouzalima/the-new-hello-world-for-faastruby-the-serverless-platform-for-ruby-crystal-part-1-2o95)。

```
$ cd faastruby
$ faastruby/> 
```

Enter fullscreen mode Exit fullscreen mode

使用您之前创建的帐户登录。([第一部分](https://dev.to/sergiosouzalima/the-new-hello-world-for-faastruby-the-serverless-platform-for-ruby-crystal-part-1-2o95))。

```
$ faastruby/>faastruby login       
Email: you@your-email.com
Password: ********
Login successful. 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 Hello World 项目

现在让我们创建名为 Hello World 的项目。

```
$ faastruby/>faastruby new-project hello-world --api 
```

Enter fullscreen mode Exit fullscreen mode

*faastruby new-project* 命令将创建 *hello-world* 文件夹，其中包含子文件夹、静态文件和初始功能。您将看到下面的输出:

```
+ d ./hello-world
+ f ./hello-world/project.yml
+ f ./hello-world/secrets.yml
+ d ./hello-world/functions/root
+ f ./hello-world/functions/root/handler.rb
+ f ./hello-world/functions/root/faastruby.yml
+ d ./hello-world/functions/catch-all
+ f ./hello-world/functions/catch-all/handler.rb
+ f ./hello-world/functions/catch-all/faastruby.yml
+ d ./hello-world/public
+ f ./hello-world/public/faastruby.yml
+ f ./hello-world/.gitignore
Initialized empty Git repository in /Users/you/workspace/faastruby/hello-world/.git/
Project 'hello-world' initialized.
Now run:
$ cd hello-world
$ faastruby local Then visit http://localhost:3000 
```

Enter fullscreen mode Exit fullscreen mode

进入我们刚刚创建的 *hello-world* 文件夹。

```
$ faastruby/> cd hello-world
$ faastruby/hello-world/> 
```

Enter fullscreen mode Exit fullscreen mode

### 文件夹&文件解释

```
├── functions <---------------- Put your functions inside this directory
│   ├── catch-all <------------ This function will run when a function is not found.
│   │   ├── faastruby.yml
│   │   └── handler.rb
│   └── root <----------------- This function will run when a request ie made to the root path '/'
│       ├── faastruby.yml 
│       └── handler.rb
├── project.yml <-------------- Project configuration file.
├── public <------------------- Put static files inside this folder.
│   └── faastruby.yml <-------- Static files configuration.
└── secrets.yml <-------------- Put your secrets here. 
```

Enter fullscreen mode Exit fullscreen mode

### 启动 faastRuby local

现在让我们启动 faastRuby 本地服务器，检查我们的项目是否真的在工作。

```
$ faastruby/hello-world/> faastruby local 
```

Enter fullscreen mode Exit fullscreen mode

您将看到输出

```
Puma starting in single mode...
* Version 3.12.1 (ruby 2.6.0-p0), codename: Llamas in Pajamas
* Min threads: 0, max threads: 32
* Environment: production
* Listening on tcp://0.0.0.0:3000
Use Ctrl-C to stop
2019-03-23 16:21:23 -0300 | Detecting existing functions.
---
2019-03-23 16:21:23 -0300 | Ruby functions: ["root", "catch-all"]
---
2019-03-23 16:21:23 -0300 | Crystal functions: []
---
2019-03-23 16:21:23 -0300 | Listening for changes.
---
2019-03-23 16:21:23 -0300 | faastRuby Local is ready at http://localhost:3000 
```

Enter fullscreen mode Exit fullscreen mode

然后，访问  。

如果一切顺利，现在浏览器中就有了*根*函数响应。

```
{
"message": "Welcome to FaaStRuby Local! Edit the function 'root' to customize this response."
} 
```

Enter fullscreen mode Exit fullscreen mode

### 创建你的第一个函数

您的第一个函数将被称为 *hello* ，它将以 json 格式响应消息“hello，World！”。

**注意**:要创建函数，必须在项目的*函数*文件夹中。

```
$ faastruby/hello-world/> cd functions
$ faastruby/hello-world/functions/> 
```

Enter fullscreen mode Exit fullscreen mode

使用 *faastruby new hello* 命令创建 *hello* 函数。

```
$ faastruby/hello-world/functions> faastruby new hello 
```

Enter fullscreen mode Exit fullscreen mode

```
+ d hello
+ d hello/spec
+ f hello/spec/spec_helper.rb
+ f hello/spec/handler_spec.rb
+ f hello/README.md
+ f hello/Gemfile
+ f hello/handler.rb
+ f hello/faastruby.yml
✔ Installing gems... 
```

Enter fullscreen mode Exit fullscreen mode

faastruby 的*new*命令，为你的 *hello* 功能创建了特定的文件夹和文件。

让我们进入 *hello* 文件夹，用 Ruby 实现我们的解决方案。

```
$ faastruby/hello-world/functions/> cd hello
$ faastruby/hello-world/functions/hello/> 
```

Enter fullscreen mode Exit fullscreen mode

使文件， *handler.rb* 看起来像:

```
# hello-world/functions/hello/handler.rb
require 'json'

def handler event
  render json: { 'message' => 'Hello, World!' }
end 
```

Enter fullscreen mode Exit fullscreen mode

我们必须修改单元测试:

```
# hello-world/functions/hello/spec/handler_spec.rb
require 'spec_helper'
require 'handler'
require 'json'

describe '#handler' do
  let(:body) { handler(event).body }
  let(:event) {
    Event.new(
      body: nil, query_params: {},
      headers: {}, context: nil
    )
  }

  context 'when function is requested' do
    let(:response_value) {
      JSON.parse(body).values.first
    }
    it 'returns a String' do
      expect(body).to be_a(String)
    end
    it "returns 'Hello, World!'" do
      expect(response_value).to eq('Hello, World!')
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要在这个函数的 Gemfile 文件中包含一个 gem。

因为我们在 *hello* 函数中使用了 *json* gem，所以我们需要将它包含在 Gemfile 中。

```
# hello-world/functions/hello/Gemfile
source 'https://rubygems.org'

gem 'json'

group :test do
  gem 'rspec'
  gem 'faastruby-rpc'
  gem 'faastruby'
end 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记运行*捆绑安装*

```
$ faastruby/hello-world/functions/hello/> bundle install 
```

Enter fullscreen mode Exit fullscreen mode

我们的单元测试通过了吗？

```
faastruby/hello-world/functions/hello/> rspec -fd 
```

Enter fullscreen mode Exit fullscreen mode

```
handler
  when function is requested
    returns 'Hello, World!'
    returns a String

Finished in 3.89 seconds (files took 0.6736 seconds to load)
2 examples, 0 failures 
```

Enter fullscreen mode Exit fullscreen mode

### 再次启动本地服务器。

**记住**:要上传本地服务器，必须在主项目文件夹中使用 *faastruby local* 命令。

因此，转到 *hello-world* 项目的根文件夹，并启动服务器。

```
$ faastruby/hello-world/> faastruby local 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看我们的 *hello* 函数是如何工作的。

在您的浏览器中访问:

您将在浏览器中看到以下结果。

```
{
"message": "Hello, World!"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 你的第一次部署

当我们将项目部署到 faastRuby 平台时，函数和静态文件就进入了工作区。

为了部署项目，必须在主项目文件夹中使用 *faastruby deploy* 命令。

因此，确保您在 *hello-world* 文件夹中，并运行 deploy 命令。

```
$ faastruby/hello-world/> faastruby deploy 
```

Enter fullscreen mode Exit fullscreen mode

```
┌ Deploying project 'hello-world' to workspace 'hello-world-stage-8f6999'
├── [✔] Connecting to workspace 'hello-world-stage-999999'
├── [✔] Uploading static assets in 'public'
├── [✔] Deploying function from 'functions/hello'
├── [✔] Deploying function from 'functions/catch-all'
└── [✔] Deploying function from 'functions/root'
* Project URL: https://hello-world-stage-999999.tor1.faast.cloud 
```

Enter fullscreen mode Exit fullscreen mode

终于，你的功能在云端可用了！您的 URL 将类似于以下 URL。

在你的浏览器中访问:

您将看到下面的输出:

```
{
"message": "Hello, World!"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

*   我们学习了如何从零开始构建 faastRuby 项目，并将其发送到云。
*   我们学习了如何使用 faastRuby 本地服务器在本地创建、测试和执行一个 Ruby 函数。

我希望你喜欢这篇文章！