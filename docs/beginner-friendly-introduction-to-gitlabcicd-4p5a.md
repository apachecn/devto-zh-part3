# GitLab CI/CD 的初学者友好介绍

> 原文：<https://dev.to/zurihunter/beginner-friendly-introduction-to-gitlabcicd-4p5a>

本教程的目标是给出 GitLab CI/CD 的高级介绍，帮助人们在 30 分钟内入门，而不必阅读 GitLab 的所有文档。本教程面向希望使用像 GitLab CI/CD 这样的 CI/CD 工具的初学者。在本教程中，我将简要介绍什么是 CI/CD，为什么我决定使用 GitLab 的工具，以及如何用一个示例应用程序创建一个`.gitlab-ci.yaml`。

### CI/CD

CI/CD 是**持续集成/持续交付/持续部署**的简称。它使团队能够以更快的速度构建、测试和发布软件。CI/CD 在可能的情况下消除了人工交互——除了将最终的人工代码部署到产品中之外，自动化了一切。实现这一实践的挑战之一是集成构建 CI/CD 管道所需的各种工具和系统。例如，您可以将代码存储在 Bitbucket 中，在私有基础设施上的自动化测试套件中进行测试，并将应用程序部署到 AWS 或 Microsoft Azure。驻留在多个系统上的复杂应用程序导致并非所有组织都实施了无缝 CI/CD 管道。

### 为什么是 GitLab CI/CD？

我使用 GitLab CI/CD 有三个原因:我可以用一个工具构建一个完整的 CI/CD 管道解决方案，速度快，而且是开源的。有了 GitLab CI/CD 在同一个地方，我可以创建票证、合并请求、编写代码和设置 CI/CD 工具，而不需要另一个应用程序。它本质上是一个一站式商店。GitLab CI/CD 跑步基于 **GitLab 跑步者**。**运行者**是独立的虚拟机，通过 GitLab CI API 运行预定义的步骤。与在单个实例上运行相比，这个工具本身允许项目更快地通过管道构建运行。你可以在这个[链接](https://docs.gitlab.com/runner/)中了解更多关于 GitLab 跑步者的细节。最后，它是开源的，所以我可以随时为代码库做贡献，并在出现问题时创建新的问题。

### 场景

假设我们有一个 Node.js API 来检索数据库中的图书列表。我们可以创建一个管道来推动我们的代码通过三个阶段:构建、测试和部署。一个**管道**是由相似特征分组的一组步骤。在这些阶段中，我们的渠道分为三种类型:

*   **项目管道**
*   **持续集成管道**
*   **部署管道**

**项目管道**安装依赖项，运行 linters 和任何处理代码的脚本。**持续集成管道**运行自动化测试并构建代码的分布式版本。最后，**部署管道**将代码部署到指定的云提供商和环境中。

这三个管道执行的步骤被称为**作业**。当你根据这些特征对一系列工作进行分组时，这被称为**阶段**。作业是管道的基本构件。它们可以按阶段组合在一起，并且阶段可以按流水线组合在一起。下面是一个作业、阶段和管道的层次结构示例:

```
A.) Build
     i. Install NPM Dependencies
     ii. Run ES-Linter
     iii. Run Code-Minifier
B.) Test
     i. Run unit, functional and end-to-end test.
     ii. Run pkg to compile Node.js application
C.) Deploy
     i. Production
        1.) Launch EC2 instance on AWS
     ii. Staging
        1.) Launch on local development server 
```

在这个层次结构中，所有三个组件都被视为三个不同的管道。主要的项目——构建、测试和部署是**阶段**,这些部分下的每个项目都是工作。让我们把它分解成一个 GitLab CI/CD `yaml`文件。

### 使用 GitLab CI/CD

要使用 GitLab CI/CD，在您的 GitLab 存储库中的项目根目录下创建一个名为`.gitlab-ci.yml`的文件，并添加下面的`yaml` :

```
image: node:10.5.0

stages:
  - build
  - test
  - deploy

before_script:
  - npm install 
```

正如我前面提到的，GitLab CI/CD 使用运行器来执行管道。通过使用`image`指令，我们可以定义我们希望我们的 Runner 基于哪个操作系统和预定义的库。在我们的实例中，我们将为我们的 Runner 使用 Node.js 的最新版本。`stages`指令允许我们为整个配置预定义一个阶段。工作将根据`stages`指令中列出的顺序执行。要了解更多关于阶段的信息，你可以点击查看[。`before_script`指令用于在所有作业之前运行命令。](https://docs.gitlab.com/ee/ci/yaml/#stages)

现在让我们从致力于**构建**阶段的工作开始。我们将把这项工作称为`build-min-code`。在这项工作中，我们希望它安装依赖关系和缩小代码。我们可以从使用`script`指令开始。`script`指令是一个在运行程序中执行的 shell 脚本。然后，我们将把这项工作分配到“构建”阶段。要将一个任务分配给一个阶段，使用`stage`指令。

```
build-min-code:
  stage: build
  script:
    - npm install
    - npm run minifier 
```

现在我们有一项与我们的**构建**阶段相关的工作，我们将为我们的**测试**阶段做这项工作。我们的测试作业将被称为`run-unit-test`，我们将在 API 中使用 npm 脚本来运行测试`npm test`。

```
run-unit-test:
  stage: test
  script:
    - npm run test 
```

最后，我们将添加一个作业来处理我们的**部署**阶段:`deploy-production`，`deploy-staging`。在本例中，我们将有两个不同的部署作业(试运行和生产)。这些工作将反映与我们以前的工作相同的布局，但有一个小的变化。目前，我们所有的任务都被自动设置为在任何代码推送或分支时触发。当我们将代码部署到产品阶段时，我们不希望出现这种情况。为了防止这种情况发生，我们使用了`only`指令。`only`指令定义了作业将运行的分支和标签的名称。该工作将如下所示:

```
deploy-staging:
 stage: deploy
 script:
   - npm run deploy-stage
 only:
   - develop

deploy-production:
 stage: deploy
 script:
   - npm run deploy-prod
 only:
   - master 
```

在我们的`deploy-staging`工作中，只有当`develop`分支和`deploy-production`分支`master`发生变化时，运行者才会执行它。下面是一个截图，显示了对`master`分支的代码推送。

[![](img/5947d331d4fca69f6b599c508df5306c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3xFO85H7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wquxay1mth6sxu4n19tt.png)

在这个图中，除了`deploy-staging`之外，所有三个阶段和作业都被触发，因为代码被推送到`master`分支。GitLab CI/CD 带有一个直观的界面，可以帮助显示正在运行的作业和阶段，以及在构建过程中出现的错误。下面是`.gitlab-ci.yaml`文件的最终版本。如果您想亲自测试一下，这里有到示例应用程序的[链接](https://gitlab.com/zh-examples/gitlabci-demo)。

```
image: node:10.5.0

stages:
  - build
  - test
  - deploy

before_script:
  - npm install

build-min-code:
  stage: build
  script:
    - npm install
    - npm run minifier

run-unit-test:
  stage: test
  script:
    - npm run test

deploy-staging:
  stage: deploy
  script:
    - npm run deploy-stage
  only:
    - develop

deploy-production:
  stage: deploy
  script:
    - npm run deploy-prod
  only:
    - master 
```

### 结论

上述内容是 GitLab CI/CD 能够提供的功能的高级概述。GitLab CI/CD 能够通过构建和发布 Docker 映像来与第三方工具集成，从而更深入地控制代码库的自动化。我希望本教程对你有所帮助。感谢阅读！