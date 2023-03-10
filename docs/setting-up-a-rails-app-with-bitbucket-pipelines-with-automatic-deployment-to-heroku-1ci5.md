# 使用 bitbucket 管道设置 rails 应用程序，并自动部署到 Heroku。

> 原文：<https://dev.to/jean182/setting-up-a-rails-app-with-bitbucket-pipelines-with-automatic-deployment-to-heroku-1ci5>

在大学里，我被分配去开发一个持续集成系统，使用任何我想使用的语言或框架，所以我决定使用 RoR。这篇文章将会强调你需要做的一些步骤来使这个工作成功。这篇文章旨在简要描述我使用 bitbucket 管道创建 CI 的所有步骤。

首先创建一个新的 rails 应用程序，我们使用-T 跳过 rails 股票测试(这里我们将使用 Rspec)并使用 postgresql 作为数据库:

```
$ rails new ci-conceptual -T --database=postgresql 
```

Enter fullscreen mode Exit fullscreen mode

对于新创建的应用程序，我通常会将以下依赖项添加到 gem 文件中

```
group :development, :test do

  gem 'pry'

  gem 'pry-remote'

  gem 'rspec-rails'

  gem 'factory_bot'

  gem 'shoulda-matchers'

  gem 'factory_bot_rails'

end

group :development do

  gem 'listen'

  gem 'web-console'

  gem 'spring'

  gem 'erb2haml'

  gem 'rails-erd', require: false

  gem 'rubocop', '~> 0.46.0', require: false

end

group :test do

  gem 'simplecov', require: false

  gem 'database_cleaner'

end 
```

Enter fullscreen mode Exit fullscreen mode

简单解释一下这个 gems，它们主要用于测试和开发目的， **rubocop** 用于 linter 检查， **ERB2HAML** 是我用来将我所有的 erb 文件转换为 haml(我更喜欢使用 haml 而不是 erb)的一个， **rails-erd** 用于基于当前数据库模式生成实体关系图 PDF 文件， **simplecov** 用于检查我们项目的测试覆盖范围，其余主要用于进行单元测试。

然后，您需要配置您的测试环境，我不会深究设置，因为这不是本文的目的。这是一个有 linter 检查和单元测试的项目，所以基本上当使用 rspec 时，你会在根目录下有一个名为 spec 的文件夹，它包含了你的应用程序的测试；大多数情况下是模型、助手、控制器、路由器和工厂。

另一方面，Rubocop 是 linter 检查所需要的，本质上你需要创建一个名为。rubocop，它将包含你想在你的项目中使用的所有规则；例如方法长度或线长度等等。他们有很多由 ruby 社区定义的[规则](https://github.com/github/rubocop-github/blob/master/STYLEGUIDE.md)，但是你可以随意使用。

在设置好之后，我们需要在 bitbucket 中创建一个 repo 来使用它们的管道，一个 **bitucket 管道**基本上是一个 docker 容器，在其中你可以重新创建应用环境，在这个例子中是一个 ruby on rails 应用。我们可以定义当我们将代码推入远程存储库时将要运行的脚本。这非常有用，因为它允许我们运行自定义脚本来检查我们的代码是否符合我们想要定义的一切，甚至是为我们的远程站点执行作业和任务。

考虑到这一点，我们需要创建一个名为 **bitbucket-pipelines.yml** 的文件，它将运行我们想要的所有脚本，更好的是，它让我们定义我们想要运行这些脚本的远程分支，如果您想要根据分支运行不同的脚本，或者更好的是，定义部署环境，这将非常有用。

在我的项目中，我有一个名为 deploy-scripts 的文件夹，其中包含可以在 docker 容器上执行的 **sh** 脚本，因为运行的是 **Ubuntu。**我没有创建脚本，但你可以在这里找到其中的一些，这是对开发者的敬意，是他们让这一切成为可能。这些脚本用于使用 bitbucket 将应用程序部署到 heroku。要做到这一点，你需要创建一个 heroku 应用程序，如果你想创建不同的环境，你也可以遵循这个[指南](https://devcenter.heroku.com/articles/multiple-environments)。

如果你已经创建了应用程序，你需要在你的存储库下进入设置/存储库 _ 变量，添加一个变量 **HEROKU_API_KEY** ，如果你安装了 HEROKU 命令行，你可以在你的终端上输入这个变量

```
$ heroku auth:token 
```

Enter fullscreen mode Exit fullscreen mode

这应该给你变量的值，你需要把名字和你的脚本中的名字完全一样，这样才能工作。考虑到这一点，您只需要配置 bitbucket-pipelines.yml 文件，该文件最终看起来像这样。