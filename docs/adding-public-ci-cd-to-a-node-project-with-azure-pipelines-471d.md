# 使用 Azure 管道向节点项目添加公共 CI/CD

> 原文：<https://dev.to/seankilleen/adding-public-ci-cd-to-a-node-project-with-azure-pipelines-471d>

我最近创建了[‘unanet-summary zer’，一个小工具，给我的同事一些关于他们的时间表](http://github.com/excellalabs/unanet-summarizer)的额外摘要信息。它得到的关注比我预期的多一点，但最重要的是，它得到了其他人的帮助，代码库迅速增长。

是时候构建和部署系统了，我爱上了 Azure DevOps，所以我想利用这个机会为我的同事和其他人写下这个过程并记录下来。

## 目标

我想为这个 JS 项目实现以下目标:

*   作为每个拉取请求的一部分运行的构建，以便我们可以检测任何损坏的更改
*   将工件输出到托管位置的生产版本(在本例中，是 Azure 上的一个简单存储 blob)
*   构建和发布的状态徽章
*   我希望任何人都能够查看构建和部署

## 预排

接下来的是一个完整的演练，包含一些挣扎，因为我希望它能清楚地告诉你什么时候可能会错过一些东西或者遇到令人困惑的步骤。

## 设置项目

*   我去[http://dev.azure.com](http://dev.azure.com)用我的 Excella 账户登录。
*   我创建了一个新项目:

> [![the create new project button on azure devops](img/422587d183abe3fe19d8461f6a43d6c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fwZWS1j8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/01_create-project.png)

*   我给它一个名字并选择选项，保持它的公开性，这样任何人都可以查看构建和发布:

> [![entering my informaton for the project](img/6247f9f6fe713145f037dbe912086548.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_IHgGpjz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/02_project-info.png)

*   在左侧导航中，我单击 pipelines，它告诉我(不出所料)不存在任何管道。我点击创建一个:

> [![new pipeline button on the pipelines page](img/a099a27dcb329ddd02ebfef3894c89b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XZB68hhl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/03_new-pipeline.png)

*   我选择 GitHub 作为代码的位置:

> [![seleting the GitHub option](img/90ed87ed1ecce5f18782daed7b28a23f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KkFiInv1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/04_select-location.png)

*   我从下拉列表中选择所有存储库(因为这不是我的 repo，而是`excellalabs`)。然后我搜索 unanet 并点击 summarizer 项目。

> [![selecting the project](img/89e4ecca1d2f5b11e4ced8ec33329699.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8z56uFvz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/05_select-repo.png)

*   我通过 GitHub 认证
*   在 GitHub 中，我被要求允许 Azure Pipelines 应用程序访问回购。我赞成。👍

> [![approving permissions](img/412cb5ad7a777423e999d0dff6ff838d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jmln7-1n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/06_grant-permission.png)

*   然后，我被要求再次验证我的 Excella 帐户。不知道为什么。

## 设置管道

*   我返回到 pipelines 页面，在这里我处于“配置”步骤，现在可以选择我想要的管道类型。我选择`node.js`因为我认为那是最合适的

> [![choosing the default type of pipeline](img/8293bcf267eae92c480299157d050b9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l6zhlY3N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/07_configure-pipeline-node.png)

*   嘿，酷，Azure DevOps 创建了一个 YAML 文件，它为我们建立了一个构建，在任何 PR 和任何我们推送至 master 的时候触发。它运行`npm install`和`npm build`。那似乎是非常正确的。

> [![the YAML file that is created for us](img/c4e100613473bde88be74ee7834d83f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1cqVuO4I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/08_pipeline-yaml.png)

*   Azure DevOps 也有这个漂亮的保存和运行按钮，它将把 YAML 文件提交回我们的 repo 并开始构建过程。所以我点击它来保存它。

> [![clicking the save and run button](img/51240ce59918bdd21ef707facd29964c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iGEXzDPR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/09_save-and-run.png)

*   我们可以选择如何承诺回购。我选择直接委身于师父，因为我活在边缘。不，开玩笑，但是我确实选择了它，因为我看到了内容，并且知道提交给 master 将允许构建开始。

> [![the set up and run screen which allows me to commit the files to the master branch](img/c36a2dc16dd292725d11b02625161c27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--59fKvTnW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/10_commit-message.png)

*   代理做好准备，然后[运行作业](https://dev.azure.com/excellaco/unanet-summarizer/_build/results?buildId=207)。成功了！我们只是还没有对输出做任何事情。

> [![build success](img/084411a7643a8ff183ae66776aa49626.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NIjfNt6y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/11_build-result.png)

## 状态徽章

接下来，我想为我可以在`README`文件中显示的构建设置一个状态标记。

*   我转到[构建定义](https://dev.azure.com/excellaco/unanet-summarizer/_build?definitionId=5&_a=summary)

*   在右边的下拉列表中，我选择了`Status Badge`:

> [![build menu dropdown](img/70d0b9f87d0432e694ec257b1f5470d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EoPzlSL---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/12_status-badge-menu.png)

*   我选择分支，然后复制提供的 markdown(这很好):

> [![copying the provided markdown](img/712bb3f8e8287eb88e4afee7437b8e7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OfV5pzGb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/13_status-badge-info.png)

*   我在这里测试 markdown:(因为为什么不呢？)

[![Build Status](img/d3258aaf606b4db9977d8ddda5e9e5bc.png)](https://dev.azure.com/excellaco/unanet-summarizer/_build/latest?definitionId=5&branchName=master)

*   不错！我将创建一个 PR 并将其添加到`README`中。

## 输出分发文件

*   我创建了一个 PR，将以下内容添加到 azure pipelines 文件中。YAML 将(我认为)获取我们构建的`dist`文件夹并输出它，但是只有当分支是主分支时。我选择了`dist`文件夹，这样我们就不需要处理`node_modules`，我只选择了`master`分支，因为只有当它是我们正在构建的主分支时，我们才会对输出做任何事情，因为那是我们将要发布的。

```
- task: PublishPipelineArtifact@0
  displayName: Publish Pipeline Artifacts
  inputs:
    targetPath: $(Build.ArtifactStagingDirectory)/dist
  condition: eq(variables['Build.SourceBranch'], 'refs/heads/master') 
```

Enter fullscreen mode Exit fullscreen mode

你知道吗？构建这个 PR 让我意识到我们从来没有在 GitHub 中为 PR 构建打开 azure 管道。让我们开始吧。

...等等，没关系，我们不需要。Azure Pipelines 已经设置好了。

> [![azure pipelines build status](img/7488aec00773a7b16b66651078b4295a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h6CyBzKS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/14_status-checks.png)

*   我看着工作在 Azure 管道上完成，它完全！....失败了。哎呀，我想我可能选错了目录？

> [![pipeline error about a path not existing](img/3407226440cdf224133a1e7c64c8f186.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pUlOLLL7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/15_artifact-fail.png)

有意思。在构建输出本身中，我看到的是`/home/vsts/work/1/s`而不是`a`。也许我用错了构建变量？

...哦，哎呀。为了发布暂存内容，我们可能必须先在那里放些东西，不是吗？所以我会在一个 PR 里加上下面:

```
- task: CopyFiles@2
  inputs:
    sourceFolder: $(Build.SourcesDirectory)/dist
    contents: '**\*'
    targetFolder: $(Build.ArtifactStagingDirectory)
  displayName: Copy Files to Staging Directory 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这确实有点奇怪。原来，变量中的构建目录似乎是`C:\agent`等。但是在 Ubuntu VM 中是`/home/vsts/work/1/s`。所以我需要硬编码以便找到文件。默认不起作用。奇怪。

...而当我换成那个的时候，还是不行。Azure Pipelines 找不到输出文件。

好吧，嗯，突然它工作了，我不知道为什么。我在日志中看到:

`Copying /home/vsts/work/1/s/dist/unanet-summarizer-release.js to /home/vsts/work/1/a/dist/unanet-summarizer-release.js`

它复制了 6000 个文件，包括节点模块等。

所以我现在要更新到从`dist`输出。一个很有意思的问题。

出于某种原因，这最终成为了完成它的任务:

```
- task: CopyFiles@2
  inputs:
    sourceFolder: '/home/vsts/work/1/s/dist' #For some reason, I think we need to hard-code this.
    targetFolder: '$(Build.ArtifactStagingDirectory)'
  displayName: 'Copy  Files  to  Staging  Directory' 
```

Enter fullscreen mode Exit fullscreen mode

我仍然不明白最终的改变是什么使它工作，但这至少对我来说是有意义的。

前进！

## 创建存储容器

注意:这只是众多方法中的一种。你可能想把文件推送到 GitHub 页面，Netlify 等等。-这只是碰巧对我有用。

下一步将是创建一个 Azure blob，然后将发布的 JS 部署到其中。

*   我使用我的凯越帐户登录 azure 门户网站
*   我导航到我们用于这些事情的资源组
*   我单击“添加”来添加资源。

> [![the add resource button](img/ed6fc587b54787b77bb1f8c6ea9e9766.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4T2Q3qhI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/16_add-resource.png)

*   我输入“存储”并选择“存储帐户”

> [![selecting the storage type](img/acd9c72a320c830424545a6152ceaa21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jE3Pyxj9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/17_select-storage.png)

*   我点击介绍屏幕上的“创建”。
*   我提供了 blob 存储的名称、区域和类型:

> [![filling in the information for our blob storage](img/9c8ae3ab9beb04c46e18fcedc8f84db5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hShnedMs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/18_storage-info.png)

*   在查看屏幕上，我单击创建。
*   创建完成后，我单击转到资源。
*   我还没有任何容器，所以我单击添加一个:

> [![creating a container](img/b6fb5cf4e9cdbd6115476bef7d12620d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aQEdhTPo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/19_add-container.png)

*   我提供了一个名称，并选择容器级匿名读访问，因为我们的目的是明确地将我们的脚本提供给全世界的人。

> [![providing information on the storage container](img/747860bd2eaaa134d671a30c638f788a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--51vFJCPT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/20_container-info.png)

*   创建容器后，我点击它。
*   然后，我单击左侧菜单上的属性，并获得[https://unanet summarizer . blob . core . windows . net/unanet-summarizer](https://unanetsummarizer.blob.core.windows.net/unanet-summarizer)的 URL:

> [![getting the URL for the blob container](img/7e6fa2e1591d815a81f55152c3cd2c01.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g8KQc5XV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/21_container-url.png)

这是我们最终部署的地方。

## 创建部署

说到这个，听起来我们应该去创建那个部署！

*   回到 Azure DevOps，我从左侧菜单中选择 [Releases](https://dev.azure.com/excellaco/unanet-summarizer/_release) 。我还没有，这是有道理的。我选择创建一个新的。

> [![screen showing no release pipelines](img/7bcad6a2aec7631e7cf70566a0624319.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_uROCz4S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/22_no-release-pipelines.png)

*   我被提示从一个模板开始，但是因为我们输出到一个 blob，我认为一个空的作业可能最有意义。

> [![selecting a job type of empty job](img/85bf915f02e5faa08102dc169bf1ea42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BIplCEjG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/23_empty-job.png)

*   我得到了一个默认的阶段(你可以为不同的环境做什么，等等。).在我们的例子中，到目前为止我们只有一个阶段:“部署到生产 blob”。所以我给这个舞台起了个名字。

> [![providing a name for the release stage](img/212a713c94aea19fd92e37e6b2aa9842.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EpNvfTS0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/24_stage-name.png)

*   我实际上还没有引入任何会启动发布的工件，所以我单击来这样做:

> [![clicking the button to add an artifact](img/d78f08a8a99bfbff19fac7a40cc8fc0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N1Wz0qm2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/25_add-artifact.png)

*   我告诉发布版本，我希望它使用来自`master`分支的最新构建的工件，然后我点击 save:

> [![providing the information for the artifact to pull in](img/37d0794b405c1bae52f94350d8b6a113.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s0BXau3H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/26_artifact-info.png)

*   注意工件上的闪电。这意味着任何时候一个新的工件出现，一个发布就会被创建和执行。

> [![showing the label of the lightning bolt, which means continuous deployment](img/9a9cd8c3af42c898652a9c42db969b41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B0opGrKe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/27_continuous-deployment.png)

*   我单击查看该阶段的任务，因为我们还没有添加任何任务:

> [![clicking the link to view stage tasks](img/4ca91797b9e78ab04a30826f9231df79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--76jKhdra--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/28_add-task-link.png)

*   我单击将任务添加到代理作业:

> [![clicking the plus icon to add a task](img/3c3bfc95f116e312cf597f659bc829b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mfAfGJZN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/29_add-task.png)

*   在任务列表中，我搜索“blob”(这是我第一次这么做)，令人惊叹的是，“Azure File Copy”出现了。我点击添加。

> [![searching for and selecting azure file copy](img/173e5484946c2d81ecd4e14f8108997e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ywcq1TTY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/30_azure-file-copy.png)

*   我看到“有些设置需要我注意”，于是我点击进入:

> [![a validation warning showing that some settings need attention](img/c3db025388856df0832f12e27d1fe9a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h-jzBT25--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/31_settings-need-attention.png)

*   我需要选择一个来源。幸运的是，有一个 elipsis 菜单，允许我根据我的工件输出选择位置:

> [![the empty source with an ellipsis to indicate more options](img/6263e05bcc8df1dc263a6a7d593f20a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jjsdHzZr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/32_source-with-ellipsis.png)

*   我选择要从中复制的工件文件夹:

> [![choosing the drop folder from a list of folders](img/8414040471e07e64c37b696964408518.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZATKG_V1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/33_select-file-from-artifacts.png)

*   我选择订阅 ID(此处省略),然后单击`Authorize`以允许 azure devops 获得所需的访问权限:

> [![the authorize button for subscription access](img/de1371eb2bb45091613322242a096853.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---vR-p6ba--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/34_authorize-subscription.png)

...我得到一个错误。这是公平的，因为我使用的是公司资源，没有完全的管理权限(这我没意见)。通常个人订阅只有 Works™️.

所以，在我的 IT 部门解除对我的封锁之前，我将暂时离开这里。

## 关于 Azure 订阅权限的说明

我们回来了！有趣事实:单击授权按钮试图对整个订阅进行授权，但是如果您单击高级选项:

> [![the dropdown button on the authorize menu that shows advanced options](img/3da855af110b01e50cf11fe5b2933b27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EcnISbR5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/35_authorize-advanced-options.png)

您可以选择一个资源组，然后它将工作，因为我有权访问该资源组:

> [![selecting a specific resource group rather than a whole subscription](img/a31f1f27b2a742c416a1555b4350e8e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R3EbQugL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/36_authorization-info.png)

...好了，回到我们的常规节目。

## 选择部署目的地并部署

*   我选择目标类型，并将其指向我创建的存储帐户:

> [![information for the storage account to push to](img/8ed68c2efbe556d2b0e978cd56596dc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zw4F8qF6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/37_deployment-destination.png)

*   好了，我想差不多就这样了，我准备保存这个版本，看看它是如何工作的。

> [![clicking the save button on the configuration](img/8635b28ff88a1aedb93269086b6091f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HmrROW8Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/38_save-release.png)

*   让我们试试吧！我进入发布页面，点击创建一个发布:

> [![clicking the button to create a release](img/8a3dcadaaccaec1998c4a9deb0032e10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gsf_Hn3G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/39_create-release.png)

*   我给这个版本一个描述，然后点击`Create`:

> [![adding a description for the release](img/d1cfba12daf04c6ed50ea5773b14755d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MLnzMusS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/40_description.png)

*   看来成功了！

> [![a release summary showing all the tasks were successful](img/328160cc6c5a9426f8147129a0f76676.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--krHJaMG1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/41_release-success.png)

*   我回到蔚蓝的门户网站查看，你瞧，它在那里！

> [![showing the file created within the blob container](img/92f5c912983dde2d235efc9b213c768e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a95JSHht--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/42_files-deployed.png)

*   只是为了检查一下，我获取了 blob 的 URL([https://unanet summarizer . blob . core . windows . net/unanet-summarizer/unanet-summarizer-release . js](https://unanetsummarizer.blob.core.windows.net/unanet-summarizer/unanet-summarizer-release.js))并在浏览器中点击它。有用！

## 添加发布徽章

现在，prod 的发布很酷，所以我想公开展示一下。我该怎么做？

*   我[在 Azure DevOps](https://dev.azure.com/excellaco/unanet-summarizer/_releaseDefinition?definitionId=1&_a=environments-editor-preview) 中打开发布定义。我单击选项、集成，启用状态标记，复制 URL，然后保存发布选项:

> [![the release integrations options screen](img/cef0c9ae3902ceab7113473fd21c90b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0Z131ZN5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/43_release-status-badge.png)

我们可以在这里查到: [![Status](img/a2b643a6d25bdad49243fc0848ca7cf5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WtmtWlY_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vsrm.dev.azure.com/excellaco/_apis/public/Release/badge/ab42bd87-c4a4-44b8-9bcc-02ab7408d6c0/1/1)

太棒了。我想我也会把它添加到自述文件中。

## 哎呀:让我们*实际上*连续部署

哎呀，最后一件事:我之前搞砸了持续部署触发器选项。当我说它将持续这样做时，我忘记了您必须首先显式地启用它(这是有意义的，我很感激。)

*   我编辑发布定义
*   我单击闪电图标，启用连续部署，并为分支添加一个过滤器:

> [![clicking the lightning bolt icon](img/ebf2b7bb329318d2f87e51f2aaf6e0a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HdquhiAB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7Bsite.post-images%7D%7D/2019-05-azure-devops-node/44_enable-continuous-deployment.png)

*   我保存发布。

...现在它会自动展开。真的真的。

## 结果

现在我明白了:

*   ✅建造完成
*   ✅版本创造了它自己
*   ✅版本适当地部署 blob
*   ✅更新 blob 上的时间戳
*   ✅`README`上的状态指示器是正确的。

就这样，我们的工作完成了！