# 集成 logrus 和 cobra

> 原文：<https://dev.to/seblegall/integrating-logrus-with-cobra-1dlb>

> 你可以在我的 GitHub 上找到一个工作示例:[https://GitHub . com/seble gall/blog-scripts/tree/master/logrus-cobra](https://github.com/seblegall/blog-scripts/tree/master/logrus-cobra)

我最近不得不重构我的一个应用程序打印日志的方式。我的想法是使用`logrus`,因为它是一个众所周知的用 Go 生成日志的库。

Logrus 允许你打印漂亮的彩色编码和结构化日志，并且与标准的库记录器完全 API 兼容。

[![logrus](img/02a8ce598932884f8e08ff4682440291.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vax-_QQ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x2085m6qng4d10mg3q7j.png)

我的第一个问题是关于日志级别，以及如何在我的项目的所有子目录/包中设置一次。

在幕后，logrus 实例化了一个新变量`log`。一旦完成，调用`logrus.SetLevel()`函数将直接在这个新实例化的结构中存储级别。

因此，我们有一个全局结构，可用于所有子包，使级别自动共享，而不必使用依赖注入。

如果你需要在一个子包中设置一个不同的级别(例如)，你只需要实例化一个新的记录器:

```
var log = logrus.New()

//...

log.Debug("debug log") 
```

> 这里有更多关于 logrus 的信息:[https://github.com/sirupsen/logrus](https://github.com/sirupsen/logrus)

然后我考虑给我的二进制文件添加一个标志，让用户设置自己的日志级别。

我的应用程序正在使用`cobra`，所以我必须根据标志值，通过设置级别来决定在哪里读取标志以及在哪里初始化记录器。

我首先想到的是创建一个可以在每个 cobra 子命令 run 函数中调用的`SetLevel()` func。但是我通过查看[斯卡福德](https://github.com/GoogleContainerTools/skaffold/blob/69776b15674898a87ac61b9431f93ee68cffa6fd/cmd/skaffold/app/cmd/cmd.go#L51-L53)的源代码发现了一个更好的方法

Cobra 有一个很酷的功能，叫做跑前跑后。它基本上是一个将在命令开始之前或之后执行的功能。

同样，您可以在 cobra 命令中定义“持久”标志(这是一种在命令和子命令之间创建继承的方法)，该框架还提供了一个`PersistentPreRun`功能。

这是放置我的日志的最佳位置:

*   为所有子命令定义一次(这可能是您在创建`verbose`标志时想要的行为。)
*   已经定义和读取标志的地方。所以我可以很好地利用用户设置的标志值。

事情是这样的:

```
package cmd

import (
    "io"
    "os"

    "github.com/sirupsen/logrus"
    "github.com/spf13/cobra"
)

//The verbose flag value
var v string

var rootCmd = &cobra.Command{
    Use:   "mycmd",
    Short: "mycmd is a test project to integrate logrus",
}

//NewMyCmd return the root cobra command
func NewMyCmd() *cobra.Command {
    //Here is where we define the PreRun func, using the verbose flag value
    //We use the standard output for logs.
    rootCmd.PersistentPreRunE = func(cmd *cobra.Command, args []string) error {
        if err := setUpLogs(os.Stdout, v); err != nil {
            return err
        }
        return nil
    }

    //Here is where we bind the verbose flag
    //Default value is the warn level
    rootCmd.PersistentFlags().StringVarP(&v, "verbosity", "v", logrus.WarnLevel.String(), "Log level (debug, info, warn, error, fatal, panic")

    return rootCmd
}

//setUpLogs set the log output ans the log level
func setUpLogs(out io.Writer, level string) error {
    logrus.SetOutput(out)
    lvl, err := logrus.ParseLevel(level)
    if err != nil {
        return err
    }
    logrus.SetLevel(lvl)
    return nil
} 
```

一旦定义了`PersistentPreRun` func，你就可以轻松地创建一个使用记录器的子命令。

```
var subCmd = &cobra.Command{
    Use:   "subcmd",
    Short: "subcmd is a subcommand of the main cmd",
    Run: func(cmd *cobra.Command, args []string) {
        runSubCmd()
    },
}

//...
//Inside the NewMyCmd() func
rootCmd.AddCommand(NewSubCmd())
//...

//NewSubCmd return the a sub cobra command
func NewSubCmd() *cobra.Command {
    return subCmd
}

func runSubCmd() {
    logrus.Debug("debug log")
} 
```

这将产生:

```
$ go run main.go subcmd -v debug
DEBU[0000] debug log 
```

默认情况下，logrus 配置在 Info 级别，我的标志的默认值是 Warn。儿子结果证明它是有效的；-)

你完了！