# Azure 管道 YAML 模板和参数

> 原文：<https://dev.to/azure/azure-pipeline-yaml-templates-and-parameters-448j>

我目前正在构建一个项目，它使用了 Azure Pipelines T1，特别是 T2 YAML Pipeline T3，这样我就可以在源代码控制中使用它。

但是管道中有许多任务，我必须用不同的参数执行多次，所以我将它们分组到一个[作业](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml&WT.mc_id=devto-blog-aapowell)中，并在需要时复制/粘贴 3 次。这是一种快速测试和运行它的方法，但是当我修改管道时，我不得不多次修改，这有点烦人(有几次我忘记复制修改并破坏构建！).

## 输入模板

过去，我在可视化管道构建器中使用[任务组](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/task-groups?view=azure-devops&WT.mc_id=devto-blog-aapowell)来提取一组要多次运行的通用任务。有了 YAML，我们有了[模板](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/templates?view=azure-devops&WT.mc_id=devto-blog-aapowell)，它允许你将`job`提取到一个你可以引用的单独文件中。

太棒了，它正如我所希望的那样工作，唯一剩下的事情就是传入各种参数。这很容易从主要管道中完成:

```
jobs:
- template: templates/npm-with-params.yml # Template reference
  parameters:
    name: Linux
    vmImage: 'ubuntu-16.04' 
```

然后就碰到了一个问题…

## 处理输出变量

并不是所有我需要传入的变量都是静态值，有些是其他任务的结果(在本例中是 ARM 部署)，这意味着我正在设置一些[多任务输出变量](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch&WT.mc_id=devto-blog-aapowell#set-a-multi-job-output-variable)。

在重构之前，我用`$[dependencies.JobName.outputs['taskName.VariableName']]`访问输出变量，一切正常，现在我需要传入它，所以我们将更新我们的模板调用:

```
jobs:
# omitted parent jobs
- template: templates/some-template.yml # Template reference
  parameters:
    STORAGE_ACCOUNT_NAME: $[dependencies.JobName.outputs['taskName.StorageAccountName']]
    name: SomeName
    azureSubscription: '...' 
```

然后在模板中我会这样使用它:

```
parameters:
  STORAGE_ACCOUNT_NAME: ''
  name: ''

jobs:
- job: ${{ parameters.name }}
  pool: 
    vmImage: 'Ubuntu-16.04'
  steps:
  - task: AzureCLI@1
    inputs:
      azureSubscription: ${{ parameters.azureSubscription }}
      scriptLocation: inlineScript
      arguments: ${{ parameters.STORAGE_ACCOUNT_NAME }}
      inlineScript: |
        account_name=$1
        key=$(az storage account keys list --account-name $account_name | jq '.[0].value')
        # more script here 
```

我运行这个程序，得到一个错误:

> az 存储帐户密钥列表:错误:存储帐户' $[依赖项。JobName.outputs['taskName。找不到 storage account name“]]”。

嗯…什么？是的，你是对的 Azure，这不是存储帐户的名称，这是你应该评估的动态变量！为什么没有评估？！

### 评估模板中的参数

问题就在这里，我传递给模板的参数没有被赋值，这意味着当我想使用它的时候，它是以原始的方式传递给脚本的。但是不要担心有一个简单的解决方法，你只需要**将参数赋给变量**:

```
parameters:
  STORAGE_ACCOUNT_NAME: ''
  name: ''

jobs:
- job: ${{ parameters.name }}
  pool: 
    vmImage: 'Ubuntu-16.04'
  variables:
    STORAGE_ACCOUNT_NAME: ${{ parameters.STORAGE_ACCOUNT_NAME }}

  steps:
  - task: AzureCLI@1
    inputs:
      azureSubscription: ${{ parameters.azureSubscription }}
      scriptLocation: inlineScript
      arguments: $(STORAGE_ACCOUNT_NAME)
      inlineScript: |
        account_name=$1
        key=$(az storage account keys list --account-name $account_name | jq '.[0].value')
        # more script here 
```

现在你在你的任务中引用了*变量*而不是*参数*。

## 结论

老实说，我在这个问题上绞尽脑汁了一天的时间，但事实并不明显。我只是在尝试了很多其他东西后偶然发现了它。

希望这能帮助其他人解决为什么他们的模板参数没有被评估的问题！