# 再给我一次机会:命令行参数

> 原文：<https://dev.to/detunized/giving-go-another-chance-command-line-arguments-3ag5>

要制作一个可用的命令行工具，首先必须解析参数。我四处寻找命令行解析库，找到了 [cli](https://github.com/urfave/cli) 和 [cobra](https://github.com/spf13/cobra) 。我试了一下 cli，然后切换到 cobra。你得跟着星星走，Github 星星。眼镜蛇有更多。

Cobra 附带了一个 cogeden 工具，我想这是一个不错的工具。因为我甚至连基本的围棋概念都还不清楚，所以我决定跑步，而不是从头开始打字。

```
$ ~/go/bin/cobra init klk
Your Cobra application is ready at
/Users/detunized/go/src/klk 
```

为什么？我在另一个文件夹里运行了它。然后我回想起，我意识到为什么我最后一次使用 go 时嘴里会有苦味。是`$GOPATH`的事情。我所知道的其他语言都没有类似的功能。显然，我不能把代码放在我想要的地方。一定是罗伯·派克想要的地方。好吧，我心想，我可以把生成的文件复制到我想要的地方，我就完成了。我做到了。

Cobra 提供了一种使用 codegen 工具添加新命令的方法，如下所示:

```
$ ~/go/bin/cobra add in
in created at /Users/detunized/go/src/klk/cmd/in.go 
```

这将添加名为`in`的子命令。要这样用:`klk in`。到目前为止一切顺利。我把新创建的文件复制到我的 repo 中，然后不停地浏览、编辑、运行，看看我的更改会有什么效果。然后我有了第一个 WTF 时刻。我修改了一些文件，但是 Go 并没有得到修改，看起来它运行的是旧的可执行文件。我花了很多时间试图找出为什么构建会无声地失败，我没有看到任何错误消息。然后我突然想到:它一直在从`$GOPATH`和我的项目文件夹中构建一些文件。因此，将文件从默认位置复制到外部文件夹并不是一个真正的选择。

好吧。但是我仍然希望我的文件在我的项目文件夹中。让我们看看将我的文件夹符号链接到`$GOPATH/src`是否会欺骗编译器:

```
$ ln -s `pwd` /Users/detunized/go/src/klk 
```

到目前为止，它工作正常，我能够使用`cobra`工具，编译器也很高兴。但是下次让我们最终进入编码。这一次没有发生。

* * *

实现这一目标的谷歌搜索:

*   转到 cli
*   go cli 解析器
*   go cli vs cobra
*   cobra 错误:Rel:不能相对于
*   gopath
*   gopath 对 goroot
*   开始程序包初始化
*   不使用 gopath 进行构建
*   没有 gopath
*   vgo

* * *

耗时:2 小时
总耗时:5 小时