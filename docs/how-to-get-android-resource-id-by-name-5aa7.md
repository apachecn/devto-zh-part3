# 如何通过名称获取 Android 资源 id

> 原文：<https://dev.to/arpytoth/how-to-get-android-resource-id-by-name-5aa7>

如果您想要编写一个依赖于资源名称而不是实际 ID 的框架，为了移除对 R 类的引用，您可能想要这样做。例如，如果你在你的类中使用了*r . layout . activity _ todo _ list*，并且你想把你的类移动到一个单独的库中…你不能。 **R 是一个生成的类**，并且是特定于项目的。一个简单的解决方案是在运行时动态加载*r . layout . activity _ todo _ list*值。幸运的是，您可以像这样轻松地做到这一点:

*context . resources . getidentifier(" activity _ todo _ list "，" layout "，context.packageName)*

如你所见，我们没有引用 R 类，所以这段代码是项目不可知的，但是:**它不安全！**如果项目中缺少资源，这段代码不会给出编译错误，因此您的应用程序可能会在运行时失败。在我的例子中，将我所有应用程序的公共代码转移到一个库中的好处比我丢失了一些资源的编译错误这一事实更重要，所以我对这种权衡很满意，但如果你没有真正好的理由，我不建议使用这种技术。

分享这个: