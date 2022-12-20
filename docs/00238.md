# 将资源从默认工作空间移动到 Terraform 上的新工作空间。

> 原文：<https://dev.to/igordcsouzaaa/migrating-resources-from-the-default-workspace-to-a-new-one-3ojc>

## 第一步——先做第一件事

我假设您使用远程状态有两个主要原因:
您应该这样做。
移动资源更加困难，所以当我解释如何在远程状态上这样做时，您将能够在本地状态上这样做。

你需要做的第一件事是删除你的后端。你大概有这样的:

```
terraform {
  backend "s3" {
    bucket = "bucket-name"
    key    = "terraform.tfstate"
    region = "us-east-1"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

不需要删，把这几行都注释掉就行了。

为什么？
因为我们需要将`tfstate`拉到本地机器上。

要将`tfstate`从远程存储拉到本地存储，我们只需要运行`terraform init`。

它将下载所有的模块并给出如下输出:

```
Do you want to migrate all workspaces to "local"?
 Both the existing "s3" backend and the newly configured "local" backend support workspaces. When migrating between backends, Terraform will copy all workspaces (with the same names). THIS WILL OVERWRITE any conflicting states in the destination.

 Terraform initialization doesn't currently migrate only select workspaces.
 If you want to migrate a select number of workspaces, you must manually
 pull and push those states.

 If you answer "yes", Terraform will migrate all states. If you answer
 "no", Terraform will abort.
Enter a value: 
```

Enter fullscreen mode Exit fullscreen mode

因为我们还没有任何其他工作空间，所以让我们设置“是”并等待。几秒钟的时间应该足以获得信息:

Terraform 已成功初始化！
现在，您可以执行`ls terraform.state`操作，您会看到文件已创建。

## 步骤 2 -设置工作区

现在我们已经在本地存储上有了状态文件，我们可以移动资源了。
如果您没有使用远程存储，您应该使用，您的工作从这里开始！

我们需要创建我们的工作空间，所以让我们运行下面的命令:

```
terraform workspace new name_of_workspace 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，我做了:

```
terraform workspace new qa 
```

Enter fullscreen mode Exit fullscreen mode

为了验证我们没有任何资源，让我们这样做:

```
terraform workspace list
  default
* qa 
```

Enter fullscreen mode Exit fullscreen mode

要检查我们是否在正确的工作区，那么运行:

```
terraform state list 
```

Enter fullscreen mode Exit fullscreen mode

您应该得到类似于:
的内容

```
No state file was found!
State management commands require a state file. Run this command
in a directory where Terraform has been run or use the -state flag
to point the command to a specific state location. 
```

Enter fullscreen mode Exit fullscreen mode

## 第三步——我们来变魔术吧！

你需要在这里多加注意。我们将使用 terraform 状态推送命令。
运行:

```
terraform state push terraform.tfstate 
```

Enter fullscreen mode Exit fullscreen mode

如果你得到一些错误，我们总是有-force 标签；)

```
terraform state push -force terraform.tfstate 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们再次运行地形状态列表:

```
terraform state list

module.qa_environment.aws_availability_zones.available
... 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步-清理

我们不能让默认的工作区拥有相同的资源，两个工作区管理相同的资源会有问题，所以让我们清理它！
首先，我们需要移动到默认工作区:

```
terraform workspace select default 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们列出所有可用的资源:

```
terraform state list
module.qa_environment.aws_availability_zones.available 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，因为我只有`module qa_environment`，我将运行这个:

```
terraform state rm module.qa_environment 
```

Enter fullscreen mode Exit fullscreen mode

这将删除所有嵌套的资源。

## 步骤 5 -将数据移动到远程存储。

让我们取消这些行的注释:

```
terraform {
  backend "s3" {
    bucket = "bucket-name"
    key    = "terraform.tfstate"
    region = "us-east-1"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

并运行

```
terraform init 
```

Enter fullscreen mode Exit fullscreen mode

将询问您是否要将所有数据移动到新的删除状态。只要按“是”就可以了！:)

## 快速提示

如果您正在使用一些 CI/CD 工具，不要忘记添加以下命令作为管道步骤:

```
terraform workspace select environment_name 
```

Enter fullscreen mode Exit fullscreen mode

如果不这样做，您将尝试在默认工作空间上重新创建所有资源，可能会出现一些错误。