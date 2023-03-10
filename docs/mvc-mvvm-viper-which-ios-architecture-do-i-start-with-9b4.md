# MVC？MVVM？毒蛇？我从哪个 iOS 架构开始？

> 原文：<https://dev.to/evandeaubl/mvc-mvvm-viper-which-ios-architecture-do-i-start-with-9b4>

当你在研究 iOS 开发时，很早你就会遇到关于 iOS 应用架构的讨论，并开始被一墙的首字母缩写词和行话所困扰:MVC、MVVM、MVP、VIPER、RIBS、Clean、RxSwift、ReactiveCocoa 等等。你还会在网上看到热烈的讨论，开发者们对他们选择的架构大加赞赏，无论是更现代的模式，如 MVVM 或 VIPER，还是久经考验的 MVC。

有了所有这些选择和所有这些观点，你会很快问这个问题:**我应该从哪一个开始？**

虽然许多其他架构都比较新，并且可以解决 iOS 开发的一些问题(如果做得正确)，但是对于您应该从哪一个开始这个问题，实际上只有一个答案: **MVC。**

之所以要从 MVC，或者说模型-视图-控制器开始，是因为它是一个成熟的架构，不仅仅是在 iOS 开发中，在一般的软件开发中也是如此。Apple 构建 UIKit 时考虑了 MVC 模式:使用 UIViews 作为视图，UIViewControllers 作为控制器(模型是另一篇文章的主题，iOS SDK 在这方面提供的指导要少得多)。

MVC 作为应用程序开发的 Apple-approved™️模式有几个好处:

*   苹果为这种模式提供了大量的第一方文档，因为它被嵌入到 SDK 中，所以许多其他开发人员和教师也是如此。
*   你不是在应对学习应用开发的双重困难**和**学习不太常见的 iOS 应用架构。
*   因为 MVC 是内置于 iOS SDK 中的，所以开发人员在代码中实现该架构的方式差异很小。其他的架构是以一种更加特别的方式开发的，每个开发人员使用它们的方式有更多的可变性。
*   许多替代模式都是基于 MVC 的，因为它们被设计来修复 MVC 的一些缺陷。
*   鉴于 MVC 是 iOS 平台的默认架构，人们期望 iOS 开发者能够理解并知道如何使用 MVC 来实现应用。

随着时间的推移，你可能会最终学会大多数(如果不是全部)这些应用程序架构，因为其中一些可能更适合某些类型的应用程序。当您刚刚起步，或者试图找出您应该最了解这些模式中的哪一个时，学习平台中的模式将是最佳选择。

* * *

你喜欢这个建议吗？下一个关于[用 gitignore](https://www.appsdissected.com/stop-changing-files-xcode-gitignore/) 停止提交 Xcode 中不断变化的文件的技巧已经在等着你了。或者[注册，让每一条建议都直接进入你的收件箱。](https://www.appsdissected.com/newsletter/)

这篇文章最初发表在 [Apps 剖析。](https://www.appsdissected.com/which-ios-architecture-pattern-to-start-with/)