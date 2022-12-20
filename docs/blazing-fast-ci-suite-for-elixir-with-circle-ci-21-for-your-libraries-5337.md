# 为您的库提供包含 Circle CI 2.1 的快速 CI 套件

> 原文：<https://dev.to/revent/blazing-fast-ci-suite-for-elixir-with-circle-ci-21-for-your-libraries-5337>

已经有一个关于这个话题的[很棒的帖子](https://mintcore.se/blog/2018/06/blazing-fast-ci-suite-for-elixir-on-circle-ci-2)(偷了标题)对我的起步帮助很大。但它也有一些缺点，使大 CI 为您的图书馆。

> 客户端代码(特定用例)和库(一般用例)有很大的区别。一个用于一个特定版本(例如`Elixir 1.8.1`)，而后一个可用于多个版本(例如`Elixir 1.8.1`用于某个新项目，而`Elixir 1.3.4`用于某个从未更新的棕色地块项目)。

如何确保您的库可以在所有支持的版本上运行？最好的方法是在所有支持的环境中进行测试。Travis CI 中有[矩阵](https://docs.travis-ci.com/user/build-matrix/)来处理这一特定用例，但他们最近做出了一些[问题](https://twitter.com/ReinH/status/1098663375985229825)T4】决策。在 Circle CI 中创建矩阵之类的东西看起来不像在 Travis CI 中那么明显，但是我让它工作了。

##### 假设

*   你知道怎么把 Circle CI 和你的回购联系起来。我刚刚授权了我的 GitHub 帐户，必须从下拉列表中选择我的回购。
*   你知道圈 CI YAML 配置放在哪里。(嘶...它在来自回购根的`.circleci/config.yml`中)

* * *

## 测试库中最新的仙丹版本

```
version: 2

# Jobs are like specific executable setups
jobs:
  # Define new job with a name
  test-1.8.1:
    # How many instances of this particular job can be run in parallel
    parallelism: 1
    # Define which docker image use as the base
    docker:
      - image: elixir:1.8.1

    # What steps the current job should execute
    steps:
      # Go to the project directory
      - checkout

      # Elixir setup
      - run: mix local.hex --force
      - run: mix local.rebar --force

      # Library setup
      - run: MIX_ENV=test mix do deps.get --only test, deps.compile, compile

      # Run tests
      - run: mix test

# Workflows are like bird view of your CI.
# Define which jobs should be executed in what orders
workflows:
  version: 2
  # Define new workflow with a name
  testing_all_versions:
    # Define which jobs should be executed
    jobs:
      # Execute job named test-1.8.1
      - test-1.8.1 
```

请查看官方文档中的[以获得更多描述和选项。](https://circleci.com/docs/2.0/configuration-reference/)

## 使它燃烧得飞快

[缓存](https://circleci.com/docs/2.0/caching/#restoring-cache)有一个很好的特性，可以大大提高作业的执行。让我们看看它的作用:

```
version: 2

jobs:
  test-1.8.1:
    parallelism: 1
    docker:
      - image: elixir:1.8.1

    steps:
      - checkout

      # Restoring state from previous executions
      - restore_cache:
          key: testing-{{ .Environment.CIRCLE_JOB }}

      - run: mix local.hex --force
      - run: mix local.rebar --force

      - run: MIX_ENV=test mix do deps.get --only test, deps.compile, compile

      # Save state after steps execution
      - save_cache:
          key: testing-{{ .Environment.CIRCLE_JOB }}
          paths:
            - _build
            - deps
            - ~/.mix

      - run: mix test

workflows:
  version: 2
  testing_all_versions:
    jobs:
      - test-1.8.1 
```

不涉及任何魔法。`restore_cache`和`save_cache`是常规步骤:

*   `save_cache` -在缓存中保存所有提供的文件夹内容
*   `restore_cache` -将缓存的文件夹放入新创建的 docker 映像中(每次执行新任务时)

中间的所有步骤都照常执行，但是例如`mix deps.get`、`mix deps.compile`和`mix compile`在第一次执行和每次连续执行时花费了多少时间？如果没有缓存的文件夹，每次作业执行都会获取依赖项，编译它们，然后从头开始编译项目。另一方面，如果这些文件夹被缓存，每次连续的时间将已经下载和编译的依赖。只会下载新的依赖项，并且只会重新编译更改过的代码。

## 测试库与多个酏剂版本的兼容性。初次尝试。

YAML 有作为[主播](https://camel.readthedocs.io/en/latest/yamlref.html#anchors)的概念，这个概念也用在前面提到的[岗位](https://mintcore.se/blog/2018/06/blazing-fast-ci-suite-for-elixir-on-circle-ci-2)上。

我的第一次尝试也是使用这种方法:

```
version: 2

jobs:
  # Added anchor with name test (&test)
  test-1.8.1: &test
    parallelism: 1
    docker:
      - image: elixir:1.8.1

    steps:
      - checkout

      - restore_cache:
          key: testing-{{ .Environment.CIRCLE_JOB }}

      - run: mix local.hex --force
      - run: mix local.rebar --force
      - run: MIX_ENV=test mix do deps.get --only test, deps.compile, compile

      - save_cache:
          key: testing-{{ .Environment.CIRCLE_JOB }}
          paths:
            - _build
            - deps
            - ~/.mix

      - run: mix test
  # another job with name
  test-1.7.4:
    # Import all steps from anchor named test
    <<: *test
    # Each property added overwrites imported properties from anchor
    docker:
      - image: elixir:1.7.4
  test-1.6.6:
    <<: *test
    docker:
      - image: elixir:1.6.6
  test-1.5.3:
    <<: *test
    docker:
      - image: elixir:1.5.3
  test-1.4.5:
    <<: *test
    docker:
      - image: elixir:1.4.5
  test-1.3.4:
    <<: *test
    docker:
      - image: elixir:1.3.4

workflows:
  version: 2
  testing_all_versions:
    jobs:
      - test-1.8.1
      - test-1.7.4
      - test-1.6.6
      - test-1.5.3
      - test-1.4.5
      - test-1.3.4 
```

基本上，我们重用以前定义的作业，并重写 docker 映像来执行步骤。现在，该库已经在所有提供的 elixir 版本中进行了测试。

虽然它对于这个特定的用例非常有效，但我还想用`mix format --check-formatted`(代码格式正确)、`MIX_ENV=test mix coveralls.circle`(测试覆盖率)、`mix credo`(代码质量)和`mix dialyzer`(类型检查)来测试最新的 elixir 版本的代码质量。对于每一种情况，我都需要更改锚定作业中定义的最后一步(`mix test`)。

锚点(据我所知)不支持部分覆盖或追加，只支持全部属性覆盖。所以对于每种情况，只有最后一步不同，我必须重写所有的步骤。这仍然会很快完成。但对我来说不是。以这种方式复制只会给未来的我更多需要小心的地方，更容易出错，而且会占用更多的精神空间。

> 不久前，我决定开始定期写博客。寻找可写的东西并不像看起来那么容易。所以主要是我试着写一些我刚刚学到的东西，希望我的发现不仅能帮助别人，也能帮助未来的我。

## 测试库与多个酏剂版本的兼容性。第二次尝试。

我决定进一步调查，并找到一个令我满意的解决方案。截至[圈 CI 2.1](https://circleci.com/changelog/#reusable-commands-and-executors-xcode-10-image-and-macos-plan-settings-updates) ，有[参数](https://circleci.com/docs/2.0/reusing-config/#using-the-parameters-declaration)这种东西。让我们用那些来代替锚:

```
version: 2.1

# Anchors are good for something tho
default_version: &default_version 1.8.1
default_steps: &default_steps
  - run: mix test

jobs:
  # Job with name build
  build:
    # Define allowed parameters
    parameters:
      # Parameter - version
      version:
        # Parameter deccription
        description: Elixir version
        # Parameter is expected to be a string
        type: string
        # Use default (latest) elixir version if none is provided
        default: *default_version
      # Parameter - execute
      execute:
        description: What steps to execute after build
        # Parameter is expected to be a list of executable steps
        type: steps
        # Use default steps if none are provided
        default: *default_steps

    parallelism: 1
    docker:
      # Image is provided dynamically, by concatenating 
      # - elixir: 
      # - the value provided via version parameter
      # 
      # Might look something like elixir:1.8.1, elixir:1.7.4
      - image: elixir:<< parameters.version >>

    steps:
      - checkout

      - restore_cache:
          # Cache key is provided dynamically by concatenating
          # - testing-elixir-v
          # - the value provided via version parameter
          #
          # Might look something like testing-elixir-v1.8.1, testing-elixir-v1.7.4
          key: testing-elixir-v<< parameters.version >>

      - run: mix local.hex --force
      - run: mix local.rebar --force
      - run: MIX_ENV=test mix do deps.get --only test, deps.compile, compile

      - save_cache:
          # Cache key is provided dynamically by concatenating
          # - testing-elixir-v
          # - the value provided via version parameter
          #
          # Might look something like testing-elixir-v1.8.1, testing-elixir-v1.7.4
          key: testing-elixir-v<< parameters.version >>
          paths:
            - _build
            - deps
            - ~/.mix

      # Execute steps provided via execute parameter.
      - steps: << parameters.execute >>

workflows:
  version: 2.1
  testing_all_versions:
    jobs:
      # Job to execute (the only job actually provided)
      - build:
          # Give it a custom name
          # Otherwise all jobs would have build-1, build-2, ... names
          name: "Test  in  elixir  1.8.1"
          # Ignore execute parameter. Will use default steps (mix test)
          # Provide version (defined in job parameters)
          # Used in
          # - defining docker image (which elixir version to use)
          # - as a cache key, so each version would have a separate cache
          #   all job calls with the same version will share cache files
          version: 1.8.1
      - build:
          # Provide a different name
          name: "Test  in  elixir  1.7.4"
          # Provide different elixir version
          version: 1.7.4
      - build:
          name: "Test  in  elixir  1.6.6"
          version: 1.6.6
      - build:
          name: "Test  in  elixir  1.5.3"
          version: 1.5.3
      - build:
          name: "Test  in  elixir  1.4.5"
          version: 1.4.5
      - build:
          name: "Test  in  elixir  1.3.4"
          version: 1.3.4 
```

如您所见，现在我们有了一个样板作业，可以用不同的参数调用它(类似于函数)。因此，如果需要，我们只需要提供灵药版本和实际的有意义的步骤。在这种情况下，我们只能提供一个版本参数作为默认步骤来执行，这是我们已经提供的`mix test`步骤。

## 测试库与多个酏剂版本的兼容性和代码质量

现在我们已经准备好实现额外的测试，这些测试与代码质量相关:`mix format --check-formatted`(代码格式正确)、`MIX_ENV=test mix coveralls.circle`(测试覆盖率)、`mix credo`(代码质量)和`mix dialyzer`(类型检查)。

```
version: 2.1

default_version: &default_version 1.8.1
default_steps: &default_steps
  - run: mix test

jobs:
  build:
    parameters:
      version:
        description: Elixir version
        type: string
        default: *default_version
      execute:
        description: What steps to execute after build
        type: steps
        default: *default_steps

    parallelism: 1
    docker:
      - image: elixir:<< parameters.version >>

    steps:
      - checkout

      - restore_cache:
          key: testing-elixir-v<< parameters.version >>

      - run: mix local.hex --force
      - run: mix local.rebar --force
      - run: MIX_ENV=test mix do deps.get --only test, deps.compile, compile

      - save_cache:
          key: testing-elixir-v<< parameters.version >>
          paths:
            - _build
            - deps
            - ~/.mix

      - steps: << parameters.execute >>

workflows:
  version: 2.1
  testing_all_versions:
    jobs:
      - build:
          name: "Test  in  elixir  1.8.1"
          version: 1.8.1
      - build:
          name: "Test  in  elixir  1.7.4"
          version: 1.7.4
      - build:
          name: "Test  in  elixir  1.6.6"
          version: 1.6.6
      - build:
          name: "Test  in  elixir  1.5.3"
          version: 1.5.3
      - build:
          name: "Test  in  elixir  1.4.5"
          version: 1.4.5
      - build:
          name: "Test  in  elixir  1.3.4"
          version: 1.3.4
  # Add aditional workflow
  validate_code_quality:
    jobs:
      # Call build job
      - build:
          # Provide custom name
          name: "Code  formatted  correclty"
          # Do not provide version parameter (use default one)
          # Provide execute parameter with custom steps
          execute:
            # This step will be executed instead of default one
            - run: mix format --check-formatted
      - build:
          name: "Code  style  check"
          execute:
            - run: mix credo
      - build:
          name: "Type  check"
          execute:
            - run: mix dialyzer
      - build:
          name: "Tests  coverge"
          execute:
            - run:
                command: MIX_ENV=test mix coveralls.circle
                environment:
                  COVERALLS_REPO_TOKEN: 5wI8JIzygEDIF4A03KNIKWdOmVK2A8dMC 
```

就是这样。现在 Circle CI 将测试所有指定 elixir 版本的库，并检查代码质量(使用格式器、credo、透析器和工作服)。

## 圆 CI CLI 工具

虽然此示例在许多情况下可以重用，但您可能经常希望实现自定义配置。每次提交和发布来查看由于错误配置而导致的 Circle CI 失败是多余且耗时的。不过不用担心，圈 CI 已经打造了一个 [cli 工具](https://circleci.com/docs/2.0/local-cli/)。

虽然它的命令很少，但最有用的似乎是`circleci config validate`，它检查配置文件是否写得正确。当你承诺和推动的时候，你就已经知道它会像预期的那样起作用。不需要 20 次提交，都是说`fixed circle ci config`(正如我在开始时所做的)。

## 结论

*   Circle CI 2.1 带来很多改进。其中之一是[参数](https://circleci.com/docs/2.0/reusing-config/#using-the-parameters-declaration)。有许多不同的方法可以重用配置部件。
*   Circle CI 速度很快，从我推送代码到所有任务执行完毕，几乎不超过一分钟。这可能与一个小的库没有足够的测试来涵盖所有内容有关。在我目前的设置中，我也不使用`credo`和`dialyzer`。谁知道呢，也许他们喜欢执行更长的时间。
*   圈子 CI 支持也快。他们在 24 小时内(周末)做出了回应。
*   Circle CI CLI 是一个很好的配套工具，可帮助您以更快的反馈循环配置 CI 环境。
*   尝试我通常领域之外的东西很有趣。虽然我到处流浪，但这是一次很棒的经历。设置非常简单，设置下一个项目将更加容易。

附言:如果您对如何改进 Circle CI 配置或博客帖子本身有任何问题或想法，请告诉我:)