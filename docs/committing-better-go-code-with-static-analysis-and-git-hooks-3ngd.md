# 用静态分析和 Git 挂钩提交更好的 Go 代码

> 原文：<https://dev.to/craicoverflow/committing-better-go-code-with-static-analysis-and-git-hooks-3ngd>

[![](img/614360443bf1687924f648094370de3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PDpHtvUT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/UkfFo63.jpg)

我使用 Go 已经有一段时间了，现在有三个静态分析工具(linters)是我日常使用的。还有几十种我可以使用的工具，但是我不认为过分依赖这些工具(大部分是非官方的)来为你工作是明智的。最终，它们将成为跟踪、维护的噩梦，并且最终可能变得无人维护或无人支持。

### Gofmt

Gofmt 是一个用于格式化 Go 代码的官方工具，自 2013 年以来，它已经成为语言的一部分。

格式化一个文件就这么简单:

```
$ go fmt main.go 
```

或者您可以将整个包作为目标:

```
$ go fmt ./pkg/models 
```

格式化当前目录下的所有包(不包括`vendor`):

```
$ go fmt $(go list ./... | grep -v /vendor/) 
```

### 错误检查

用制作者自己的话说， [errcheck](https://github.com/kisielk/errcheck) “检查你检查的错误”。换句话说，它会扫描你的代码，并告诉你是否有任何错误。这对于那些在做了一个小小的改变后愚蠢地忘记运行应用程序的时候很有用。

检查包中的错误:

```
$ errcheck ./pkg/models 
```

或者扫描当前目录下的所有包(不包括`vendor`):

```
errcheck $(go list ./... | grep -v /vendor/) 
```

### Vet

Vet 是 Go 中的另一个官方静态分析工具。Vet 检查源代码中的可疑结构，比如参数与格式字符串不一致的 Printf 调用。

它的执行与 Gofmt 非常相似。

审查单个文件:

```
$ go vet main.go 
```

检查整个包:

```
$ go vet ./pkg/models 
```

审核当前目录下的所有包(不包括`vendor`):

```
$ go fmt $(go list ./... | grep -v /vendor/) 
```

## 用 Git 挂钩自动化这些工具

[Git 挂钩](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)是自动化这些重复任务的好方法，确保你不会提交或推送任何不完整的代码。这些钩子都存储在 Git 项目的`.git/hooks`子目录中，并将在预定义的 Git 事件列表上或之后自动执行。

为此，我们将绑定到`pre-commit`钩子，因为我们只希望我们的脚本在将文件提交给 Git 之前检查它们。

在`.git/hooks`中创建一个名为`pre-commit`的新文件，并在您最喜欢的编辑器中打开它。通过从终端运行`chmod +x .git/hooks/pre-commit`使其可执行。

首先，让我们将项目中的文件列表存储到一个名为`FILES` :
的变量中

```
FILES=$(go list ./...  | grep -v /vendor/) 
```

在格式化您的代码旁边添加 Gofmt】

```
go fmt ${FILES} 
```

一旦格式化，我们需要检查的最重要的事情是是否有任何错误。如果有，我们可以退出脚本并阻止提交。

```
{
    errcheck -ignoretests ${FILES}
} || {
    exitStatus=$?

    if [ $exitStatus ]; then printf "\nErrors found in your code, please fix them and try again."
        exit 1
    fi
} 
```

如果代码没有错误，接下来我们应该检查是否有任何可疑的构造。

```
{
    go vet ${FILES}
} || {
    exitStatus=$?

    if [ $exitStatus ]; then printf "\nIssues found in your code, please fix them and try again."
        exit 1
    fi
} 
```

这里是完整的`pre-commit`钩子。现在就把它添加到你的项目中，开始提交更好的 Go 代码。