# 学习 Go 注释-交叉编译

> 原文：<https://dev.to/haruanm/learning-go-notes-crosscompiling-5ae2>

作为我学习 Go 的一部分，昨天我试着从我的 ubuntu linux 编译一个简单的项目到 windows，我已经得到了一些小技巧。

1 -要进行交叉编译，您只需更改两个环境变量，GOARCH(处理器的架构)和 GOOS(操作系统)，您可以通过使用 export 来实现:

export go arch = " amd64 "

export GOOS = " windows "

但是这使得您需要稍后恢复它。

对于单行方法，您可以在运行构建命令之前传递参数。

`GOOS=windows GOARCH=386 go build`

2 -如果你在 cgo 中使用一些 C 库，你需要启用 CGO 启用标志，并有适当的结构来编译 C 库(windows 的 mingw 和类似的东西)。例如:

`GOOS=windows GOARCH=386 CGO_ENABLED=1 CC=i686-w64-mingw32-gcc CXX=i686-w64-mingw32-g++ go build -ldflags "-H windowsgui"` < =这个没用。

在某些情况下，你可以使用 xgo([https://github.com/karalabe/xgo](https://github.com/karalabe/xgo)):

`xgo --targets=windows/* .`会编译成 windows。

交叉编译相对容易，让我们稍后检查更大的项目。如果你有什么案例要讲或者有什么建议，欢迎评论。