# circleci 上的平行测试

> 原文：<https://dev.to/zygo-tecnologia/parallel-tests-on-circleci-4k4i>

[![](img/c242ad9647f2b27f69503ccd03e2ceda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xE3gEROK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASatWV791L3emuqDLKi-B4A.jpeg)

当您有一个大的代码库时，测试套件在开始时运行不到 5 分钟，开始需要超过 30 分钟。这就是我们主要项目的情况，我们有一个自动构建，运行 brakeman、rubocop、bundler audit 和 circleci 上配置的测试，为 github 上打开的每个 PR 和推送到 master 的每个 commit 运行，构建花了 40 多分钟运行。

这一直困扰着我，但在最近几周，我开始对此感到更加困扰。这种变化的主要原因是，我们改变了 SumOne 的一些实践和文化，使开发团队更加关注最终的交付，并创建一个更加敏捷的团队，一个更快的构建和交付过程，这样我们就能够为我们的客户提供更多的价值。因此，我们的开发人员现在关心并参与我们流程的每一步，从发现到交付，并且必须等待 40 多分钟才能批准您的拉式请求进行合并，这在所有这些过程中并不是一个敏捷和快速的流程。

于是，我开始思考如何解决这个问题。几个月前，我试图使用一个 gem 在本地并行运行测试，但是效果不好。所以，我决定只有一个目标，如何让构建更快，测试在 circleci 上并行运行，现在放弃更快的本地测试。

首先，你需要决定你想要多少个并行作业，不幸的是 circleci 不允许自由账户并行。因此，举例来说，如果您决定并行运行 2 个作业，您将需要添加一个付费容器，成本为 50 美元。之后，只需配置。具有并行性的 circle ci/config . yml:2。

下一步是为每个容器运行不同的 rspec 文件。在寻找 circleci 的文档和支持对话时，我在。circleci/config.yml 项目文件:

```
- run:
  name: Tests
  command: |
    TESTFILES=$(circleci tests glob "spec/\*\*/\*.rb" | circleci tests split --split-by=timings)
    bundle exec rspec -- ${TESTFILES} 
```

Enter fullscreen mode Exit fullscreen mode

第一行首先使用 circleci tests glob 命令获取 spec 文件夹中的所有文件，然后使用 circleci tests split 命令将其拆分。有一些选项来决定如何分割测试，你可以在文档中找到它[。我决定使用- split-by=timings 来让 circleci 注意并找到更快运行测试的最佳方法。第一行的输出是一个文件列表，它是运行您的构建的每个 circleci 容器的不同列表。](https://circleci.com/docs/2.0/parallelism-faster-jobs/#splitting-test-files)

对于习惯于 rspec 测试库的 rails 开发人员来说，第二行更容易理解。我们只是调用 rspec 命令，并向它传递文件列表，这样我们就可以让每个容器运行不同的测试。

更简单对吗？差不多！做完这些后，我发现了第一个问题。我们在项目中使用 factory_bot，circleci 测试 glob "spec/**/*。rb 正在获取 spec/factories 文件夹中的文件，当它作为参数传递给 bundle exec rspec 时，rspec 试图运行 factories 文件，我们得到了一个 Factory FACTORY_NAME 已经注册的错误，没有任何工作。

所以，再看一下 circleci 文档，我发现了这一节,其中显示了允许您将 circleci 测试 glob 作为参数传递以过滤文件的选项。所以，我又改了。circleci/config.yml 文件，结果是:

```
- run:
  name: Tests
  command: |
    TESTFILES=$(circleci tests glob "spec/{controllers,features,helpers,listeners,mailers,models,services,workers}/\*\*/\*.rb" | circleci tests split --split-by=timings)
    bundle exec rspec -- ${TESTFILES} 
```

Enter fullscreen mode Exit fullscreen mode

所以，现在 circleci 只过滤真实的测试文件，并把它作为参数传递给 rspec 命令。在提交并将其推送到 github 之后，circleci 再次运行它，花费了 40 多分钟的测试套件开始在不到 10 分钟的时间内运行。

最后一件事，我决定对 rubocop 任务也这样做，所以我将这些行添加到配置文件:

```
- run:
  name: Rubocop
  command: |
    PROJECTFILES=$(circleci tests glob "app/\*\*/\*.rb" | circleci tests split --split-by=timings)
    bundle exec rubocop -- ${PROJECTFILES} 
```

Enter fullscreen mode Exit fullscreen mode

Rubocop 任务时间不是一个大问题，但是因为我是为 rspec 做的，所以没有理由不使用 rubocop 做这件事，结果是一个几乎需要 2 分钟的任务现在只需要不到 20 秒就可以运行了。

因此，这是改进我们的过程并让构建更快的简单方法，下一步是真正改进我们的测试，尝试让每个测试运行得更快。

* * *