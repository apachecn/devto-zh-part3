# 移植到 Swift？使用扩展逐步完成:第 1 部分

> 原文：<https://dev.to/evandeaubl/porting-to-swift-do-it-gradually-using-extensions-part-1-188h>

尽管 Swift 已经向 iOS 开发者开放了 4 年多，但很可能许多长期存在的应用程序中仍然有 Objective-C 代码。为了保持最新，并利用更现代的 Swift 语言，您需要将这些类中的至少一部分移植到 Swift。

你会立刻被你的小而简单的课程吸引，你会想，“当然，这些根本不是问题。我可以在几个小时内完成一个。”

但是你开了一个大班。你被冻住了。

你知道是哪一个。每个代码库(至少)都有一个。它是应用程序的核心类之一:数百(或数千……)行代码，许多依赖于你的代码库中的其他类。这是应用程序的核心部分；移植这个类并测试移植不会完全破坏其他所有东西可能需要几周时间。没人再有时间过“几周”了。

所以你投降了，那些又大又毛的 Objective-C 类仍然是又大又毛的 Objective-C

它节省了不必要的返工，只重构您实际上因为另一个原因而更改的代码。但是如果你只改变一个方法，你不会想移植你没有接触到的类的其余部分，因为这意味着额外的编码工作，额外的测试工作，额外的审查工作...

**有没有这样移植 Swift 的方法，一个方法接一个方法，一个属性接一个属性？有，通过为你的 Objective-C 类编写 Swift 扩展。**

* * *

扩展允许您创建一个单独的代码块，用其他方法扩展现有的类定义。您可以使用此功能将您的 Objective-C 代码逐步移植到 Swift，并尽可能减少中断。下面的过程直接来自于我为自己的一个应用程序所做的移植工作，这个应用程序是一个名为 12 Strikes 的保龄球分数追踪器。

首先，如果这是您第一次移植一个特定的类，您将需要为该类的扩展创建一个新的 Swift 文件，这是您在 Xcode 中创建新的 Swift 文件的标准方式。之后，如果您的 Swift 代码需要引用 UIKit，请在顶部添加`import UIKit`。

一旦有了`.swift`文件，添加空的扩展定义:

```
extension BowlingGame {
} 
```

您应该已经有了一个`Your Project-Bridging-Header.h`，或者来自前面的步骤，或者来自前面的移植工作。如果没有，按照[这些指示](https://stackoverflow.com/questions/31097484/creating-a-bridging-header)手动创建一个。一旦有了一个，就为要移植的类添加匹配的 Objective-C `.h`文件，这样 Swift 就可以引用基本 Objective-C 类中的任何内容:

```
#import "BowlingGame.h" 
```

现在您可以着手移植您的方法了。首先，注释掉 Objective-C 方法:`.m`文件中的代码定义和`.h`文件中的声明。

此时，喘口气，让 Xcode 重新构建您的项目。你的编译成功了吗？如果是这样，这是一个很强的指标，表明你不需要移植这段代码，你只需要删除它。如果你不使用它，就不要移植它。

如果您确实得到了您应该得到的编译错误，现在在您的 Swift 扩展中重写您的方法。确保将`@objc`修饰符应用到方法定义中，以便可以从 Objective-C 中访问该方法:

```
@objc public static func scoreFrame(firstRoll: String, secondRoll: String) -> Int 
```

如果你有这个方法的单元测试，你也可以重写这些测试，但是我建议不要这么做。你试图一次重写尽可能少的内容。如果你也有重写测试的冲动，在你将方法移植到 Swift 并确保 Swift 方法通过了所有的 Objective-C 测试之后**重写它们。Objective-C 测试将能够测试您移植的 Swift 方法。**

最后，如果您有依赖于移植的 Swift 方法的 Objective-C 方法，那么您还需要添加一个`#import`来将 Swift 声明导入到引用它的任何`.m`或`.h`文件中:

```
#import "Your_Project-Swift.h" 
```

一旦你移植了这个方法，你就可以运行这个应用程序来检查你是否正确地移植了代码(特别是如果你没有单元测试的话……)，一旦你满意了，就删除注释掉的 Objective-C 版本。

搞定了。从那里开始，简单地重复，直到整个类被移植。虽然可能不是马上。记住，你只改变端口代码！

现在是细则:

许多类可以很容易地进行移植，但是有一些特殊的情况需要一些特殊的技术，以便以一种方法接一种方法的方式从 Objective-C 移植到 Swift:使用高级 Swift 数据结构和 UIKit。我将在本系列的第 2 部分和第 3 部分的[应用剖析](https://www.appsdissected.com)中讨论这些。[注册订阅时事通讯，以便在这些部件发布时得到通知。](https://www.appsdissected.com/newsletter/)

* * *

你喜欢这个建议吗？本系列关于增量快速移植的第 2 部分已经在等你了。或者[注册，让每一条建议都直接进入你的收件箱。](https://www.appsdissected.com/newsletter/)

这篇文章最初发表在 [Apps 剖析。](https://www.appsdissected.com/port-objective-c-swift-extensions/)