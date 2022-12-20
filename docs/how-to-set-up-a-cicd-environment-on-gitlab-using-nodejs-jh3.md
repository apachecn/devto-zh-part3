# 如何使用 NodeJs 在 Gitlab 上设置 CI/CD 环境

> 原文：<https://dev.to/6thcode/how-to-set-up-a-cicd-environment-on-gitlab-using-nodejs-jh3>

所以我决定通过讨论如何建立持续集成(CI)和持续交付(CD)来开始我关于 dev.to 的第一篇文章，欢迎反馈，这很有帮助！

[![](img/2e7b2d0f0033cc3563b8939ee3854bc1.png)](https://i.giphy.com/media/3o7TKsJEd0lp7GNUpq/giphy.gif)

持续集成(CI)是一种实践，它要求开发人员多次将代码集成到共享存储库中。然后，每个签入都由一个自动构建进行验证，允许团队检测问题并尽早修复。

连续交付(CD)是一种软件工程方法，其中连续集成、自动化测试和自动化部署功能允许在最少的人工干预下开发和部署软件。

拥有一个合适的 CI/CD 环境将能够更早地发现错误，开发人员可以更快地检查代码并修复它。

到本文结束时，您将对如何在 Gitlab 上设置 CI/CD 环境有了基本的了解。

### 第一步:在 Gitlab 上创建项目

Gitlab 是一个基于 web 的 Git-repository manager，可以在其中托管代码。如果您没有帐户，请注册；如果您已经有帐户，请登录现有帐户。创建一个存储库，你可以给它起任何你喜欢的名字，你也可以选择公开或者私有存储库。公众可以通过搜索或使用项目的 URL 直接访问公共存储库，相反，私有存储库只能由所有者或维护者邀请的成员访问。

### 步骤 2:设置您的 CI 环境

要设置一个 CI 环境，您需要创建一个名为`.gitlab-ci.yml`的文件，它应该位于您的存储库的根目录下。

该文件包含如何构建项目的分步描述。Gitlab runner 将在您的存储库中搜索这个文件并执行它。GitLab CI 在您的存储库中查找这个特定的文件，以确定它应该如何测试代码。

您可以通过已经创建的 Gitlab 项目或通过您的终端来创建这个文件。我将在本教程中使用终端。我喜欢我的 bash 命令😏😏

打开您的终端，移动到项目文件所在的目录，然后通过以下操作创建一个名为`.gitlab-ci.yml`的文件:-

`$ touch .gitlab-ci.yml`

接下来，我们必须编辑`.gitlab-ci.yml`文件，这样我们就可以通过以下操作创建 CI 配置:-

`$ nano .gitlab-ci.yml`

配置项配置看起来像这样，应该在您的`.gitlab-ci.yml`文件中:-

```
 image: node:latest

    stages:
      - build
      - test

    cache:
      paths:
        - node_modules/

    install_dependencies:
      stage: build
      script:
        - npm install
      artifacts:
        paths:
          - node_modules/

    testing_testing:
      stage: test
      script: npm test 
```

Enter fullscreen mode Exit fullscreen mode

让我们来理解上面的片段是关于什么的

[![](img/3dce533a12f7312249921d74f2a6ab57.png)](https://i.giphy.com/media/aBN66chr4Y9na/giphy.gif)

配置文件从声明 docker 映像开始，该映像允许您指定在构建时要使用的 NodeJS 的某个版本。

```
 image: node:latest 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们定义它将运行的不同的持续集成过程。

```
 stages:
      - build
      - test 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经定义了阶段，配置包括一个`cache`，它指定了应该保存的文件，以便在运行或阶段之间使用。

```
 cache:
      paths:
        - node_modules/ 
```

Enter fullscreen mode Exit fullscreen mode

接下来是`install_dependencies`，在演示阶段间交互的过程中，我们正在抽取这个步骤来运行它自己的阶段。通常，运行`npm install`可以与接下来的测试阶段相结合。

```
 install_dependencies:
      stage: build
      script:
        - npm install
      artifacts:
        paths:
          - node_modules/ 
```

Enter fullscreen mode Exit fullscreen mode

最后，`testing_testing`声明将运行测试套件的命令，因为这是最后一个阶段，它将访问由`build`阶段产生的内容，在我们的例子中是项目依赖关系。

```
 testing_testing:
      stage: test
      script: npm test 
```

Enter fullscreen mode Exit fullscreen mode

哦，是的，`testing_testing`只是我用的一个名字，你可以给它起任何你想起的名字。

### 第三步:安装 Gitlab Runner

由于我们的存储库包含一个`.gitlab-ci.yml`文件，任何新的提交都将触发新的 CI 运行。如果没有可用的运行程序，CI 运行将被设置为“待定”。

如前所述，在 GitLab 中，跑步者执行您在`.gitlab-ci.yml`中定义的任务。我将遵循这里的指示。

### 第四步:注册 Gitlab Runner

接下来，按照此处的说明[注册 Gitlab Runner。
在 shell 上运行 runner executor 是最容易设置的，所以在注册之后，用下面的命令安装并启动服务:](https://docs.gitlab.com/runner/register/index.html)

`$ gitlab-runner install
$ gitlab-runner start`

要确保您的跑步者正在跑步，请运行以下命令:

`$ gitlab-runner status`

如果你看到这个:`gitlab-runner: Service is running!`和你在 gitlab 上创建的项目上的绿色勾号

[![Alt gitlab screenshot](img/38f7f27790c86846aed5f3df1a63cc0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--khfvJ69n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0defp5cgmdjb69b6yrz9.png)

### 结论

希望这篇文章拓宽了 Gitlab CI/CD 的边界，丰富了您的理解，就像我之前说的，欢迎反馈，万一我错过了什么，请通知我。