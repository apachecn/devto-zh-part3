# Swift 4.2.1 有什么新功能？

> 原文：<https://dev.to/jarroo/whats-new-in-swift-421-1gog>

是啊！似乎有这样一个东西，它与 Xcode 10 的第一个点发布版本 [10.1](https://developer.apple.com/documentation/xcode_release_notes/xcode_10_1_release_notes) 一起被默默发货。与主要版本相反，Swift 4.2.1 的发布没有伴随着[博客](https://swift.org/blog)帖子，其条目也没有被添加到 [Swift 变更日志](https://github.com/apple/swift/blob/master/CHANGELOG.md)。这一切的神秘是有原因的，还是 4.2.1 真的如此微不足道？

根据他最近在 [Swift 论坛](https://forums.swift.org/t/swift-4-2-1/17553)上的帖子，Pierpaolo Frasa 一定也在想同样的事情:

> 我的 mac 希望我升级到 Xcode 10.1，这将包括 Swift 4.2.1。然而，无论是在下载页面还是在变更日志中，我都找不到任何关于 swift 版本的信息。
> 
> [ [...](https://forums.swift.org/t/swift-4-2-1/17553) ]

已解决问题的列表相当短，修复了 8 个编译器问题，在标准库中只解决了一个问题。亮点包括:

*   修复了类似“命令 CompileSwiftSources 因非零退出代码而失败”的模糊编译错误消息
*   Bundle 类的 init(for:)初始化器现在与 Swift 类一致，而不仅仅是 Objective-C 类
*   包含空格的长文件路径不再导致构建失败
*   根据协议类型的值返回 Self 的变异方法现在可以成功调用了

查看 [Xcode 发行说明](https://developer.apple.com/documentation/xcode_release_notes/xcode_10_1_release_notes)中的“Swift 编译器”和“Swift 标准库”部分，获得完整的变更列表，包括代码样本。