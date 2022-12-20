# xerrors 关于痕迹细节的特征较少

> 原文：<https://dev.to/ques0942/xerrors-has-less-feature-about-traces-detail-4861>

这是对 Go 2 提案的反馈。这是我的看法。如果您对提案有任何意见，请[反馈](https://go.googlesource.com/proposal/+/master/design/go2draft.md)您的意见。

对我来说，Go 2 误差值的建议看起来不错。检查和打印功能简单易用。但不足以读取错误堆栈跟踪。
有些情况(我用[哨兵](https://sentry.io/welcome/)的时候遇到过)，我们想读取错误堆栈跟踪(即。程序计数器，函数名，行号...等等)来创建结构化的错误数据。该建议(“xerrors”)实现仅提供用于打印错误的打印机接口。
这个接口专注于打印和格式化，所以我们不能用它来读取 stacktrace。如果我想用“xerrors”读取 stacktrace，我必须写[脏代码](https://gist.github.com/ques0942/d2e5e55780b08653adde602ffb247925)。
如果“xerrors”将显示 stacktrace 或返回 runtime。框架，会很有用的。所以我支持[这个想法](https://gist.github.com/axw/247b6f69d2da016bb21a5eb1be44e611)。