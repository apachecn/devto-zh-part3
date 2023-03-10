# 让您的 iOS 项目保持健康

> 原文：<https://dev.to/sadmansamee/keep-your-ios-projects-healthy-1ao4>

保存您的代码和项目是您可以对您的项目做的最好的事情，以下是如何在您用 swift 编写的 iOS 项目中做到这一点。

## 遵循风格指南

写 swift 有几个风格指南，每个团队成员都应该很好地遵循它们。诸如

1.  [Raywinderlich 风格指南](https://github.com/raywenderlich/swift-style-guide)
2.  [Linkedin 风格指南](https://github.com/linkedin/swift-style-guide)
3.  [谷歌风格指南](https://google.github.io/swift/)

## 使用工具

实际上，不是每个人都做得很好，所以应该有一种方法在团队成员中强制执行，一种方法是手动代码审查，这非常耗时，代码审查应该发生在逻辑级别，而不是开发人员应该如何声明变量或缩进，我们可以简单地自动化这些事情，并在更难的事情上投入更多时间。

1.  [Swiftlint](https://github.com/realm/SwiftLint) 将出色地识别 swift 代码。
2.  你也可以用 [xiblint](https://github.com/lyft/xiblint) 为故事板和 xib 文件添加林挺
3.  最好有一种格式化代码的方式，比如缩进多少，声明变量的方式等等，这就是 [Swiftformat](https://github.com/nicklockwood/SwiftFormat) 真正出色的地方。
4.  为了消除未使用的 Swift 代码，转到[外围](https://github.com/peripheryapp/periphery)
5.  对你的代码进行静态分析是减少 bug 的一个好方法， [Infer](http://fbinfer.com) 是一个静态分析工具，可以产生潜在 bug 的列表。
6.  你也可以使用 danger 在[持续集成](https://www.thoughtworks.com/continuous-integration)级别上自动使用这些工具。你可以用危险来编纂你的团队规范。让人类去思考更难的问题。而[危险](https://danger.systems/ruby/)支持 Circle、Travis、Jenkins、Buildkite、BuddyBuild、Semaphore、TeamCity、Xcode Bots、Drone、Surf 和 Bitrise。

## 整理 Xcode 项目

在 Xcode 的旧版本中，你只能分组，并且在下面没有文件夹结构，所以基本上每个文件都默认保存在一个文件夹中，这很混乱！

1.  你可以使用命令行工具 [Synx](https://github.com/venmo/synx) 根据 Xcode 组创建文件夹。
2.  有时您的 Xcode 项目可能会损坏，您可以使用 [XcodeGen](https://github.com/yonaskolb/XcodeGen) (一种用于生成 Xcode 项目的 Swift 命令行工具)根据文件夹结构取回您的 Xcode 项目。