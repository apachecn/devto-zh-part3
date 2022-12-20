# 介绍 faastRuby 的 hello world-ruby 和 Crystal 的无服务器平台-第二部分。

> 原文：<https://dev.to/sergiosouzalima/reapresentando-o-hello-world-para-faastruby-a-plataforma-serverless-para-ruby-e-crystal-parte-ii-31c5>

[![alt text](img/af1fd29649cee0c1ad3d50cc17c574ee.png "faastRuby logo")](https://res.cloudinary.com/practicaldev/image/fetch/s--2IyWzhJz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://faastruby.io/wp-content/uploads/2019/03/logo-negative-1.png)

你好，开发商朋友们！这是文章的第二部分。如果你想了解 faastRuby 的基本知识，或者还没有在平台上注册，请从这里开始[第一部分](https://dev.to/sergiosouzalima/reapresentando-o-hello-world-para-faastruby-a-plataforma-serverless-para-ruby-e-crystal-parte-i-ac0)。

我们要创造什么？
利用命令和 serverless 平台[【faastRuby】](https://www.faastruby.io)，我们将在 ruby 中创建一个函数，并在互联网上快速提供。

## 项目创建的逐步

### 登录

前面有一份意大利面。

```
$ cd faastruby
$ faastruby/> 
```

Enter fullscreen mode Exit fullscreen mode

使用在本文的[第 I](https://dev.to/sergiosouzalima/reapresentando-o-hello-world-para-faastruby-a-plataforma-serverless-para-ruby-e-crystal-parte-i-ac0) 部分中创建的帐户登录。

```
$ faastruby/>faastruby login       
Email: you@your-email.com
Password: ********
Login successful. 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 Hello World 项目

接下来，我们将创建 Hello World 项目。

```
$ faastruby/>faastruby new-project hello-world --api 
```

Enter fullscreen mode Exit fullscreen mode

命令 *faastruby new-project* 将创建文件夹*【hello-world】*，其中包含初始子文件夹、静态文件和函数。你会看到下面的消息

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

进入我们刚刚创建的文件夹*【hello-world】*。

```
$ faastruby/> cd hello-world
$ faastruby/hello-world/> 
```

Enter fullscreen mode Exit fullscreen mode

### 文件夹和文件的含义

```
├── functions <---------------- Todas functions ficam dentro desta pasta
│   ├── catch-all <------------ Se uma function não é encontrada, esta function é executada.
│   │   ├── faastruby.yml
│   │   └── handler.rb
│   └── root <----------------- Requisições em "root path" '/' caem aqui.
│       ├── faastruby.yml <---- Configuração de arquivos estáticos.
│       └── handler.rb
├── project.yml <-------------- Arquivo de configuração do projeto.
├── public <------------------- Aqui dentro ficam arquivos estáticos.
│   └── faastruby.yml
└── secrets.yml <-------------- Seus secrets ficam aqui. 
```

Enter fullscreen mode Exit fullscreen mode

提升本地服务器
现在让我们提升本地服务器，验证我们的项目是否正在运行。

```
$ faastruby/hello-world/> faastruby local 
```

Enter fullscreen mode Exit fullscreen mode

你会看到消息

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

然后访问地址:http://localhost:3000

如果到目前为止一切顺利，您将在浏览器中看到功能 *root* 的结果。

```
{
"message": "Welcome to FaaStRuby Local! Edit the function 'root' to customize this response."
} 
```

Enter fullscreen mode Exit fullscreen mode

### 创造其第一个功能

它的第一个功能将被命名为*，并以 json 格式回复消息“hello，World！”。*

 ***重要**:要创建函数，必须位于项目的 *functions* 文件夹内。

```
$ faastruby/hello-world/> cd functions
$ faastruby/hello-world/functions/> 
```

Enter fullscreen mode Exit fullscreen mode

使用 o comando*faastruby new hello*para criar a function*hello*。

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

*faastruby new* 命令为其功能 *hello* 创建了特定的文件夹和启动文件。
我们将进入*【您好】*文件夹，在 Ruby 实施我们的解决方案。

```
$ faastruby/hello-world/functions/> cd hello
$ faastruby/hello-world/functions/hello/> 
```

Enter fullscreen mode Exit fullscreen mode

使其功能*保持这样:* 

```
# hello-world/functions/hello/handler.rb
require 'json'

def handler event
  render json: { 'message' => 'Hello, World!' }
end 
```

Enter fullscreen mode Exit fullscreen mode

我们要调整单元测试:

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

现在，我们需要在此函数的 Gemfile 文件中包含 gem。我们在函数*内使用 gem *json* ，因此需要将其包含在 Gemfile 中。* 

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

不要忘记*捆绑安装*

```
$ faastruby/hello-world/functions/hello/> bundle install 
```

Enter fullscreen mode Exit fullscreen mode

我们的测试通过了吗？

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

再次上传本地服务器

请记住，要提升本地服务器，必须在项目的主文件夹中使用命令*本地 faastruby】。*

因此，转到项目根文件夹*【hello-world】*并向上移动服务器。

```
$ faastruby/hello-world/> faastruby local 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看我们的功能*你好*的运作情况。

visite o ender EO:

您将在浏览器中看到功能 *hello* 的结果。

```
{
"message": "Hello, World!"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 首次部署

为 faastRuby 平台执行项目部署时，静态文件和函数将转至 Workspace。

要部署项目，必须在项目主文件夹中使用命令*【faastruby deploy】*。

因此，请确定您位于 *hello-world* 资料夹中，并执行部署。

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

最后，它的功能在云中可用！您的 URL 将如下所示。

参观当地的 no seu 浏览器:

您将在浏览器中看到功能 *hello* 的结果。

```
{
"message": "Hello, World!"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

*   在本文中，我们学习了如何从头开始在 faastRuby 构建一个项目，并将其发送到云中。
*   我们学习了如何创建自己的函数、创建单元测试以及使用本地服务器运行函数。

希望你们喜欢这篇文章！***