# 第 009 天:制作待办事项清单

> 原文：<https://dev.to/maeganwilson_/day-009-making-a-todo-list-1nbe>

[GitHub 回购](https://github.com/maeganjwilson/todo_list)

第九天的大部分时间，可能还有第十天，将会研究并尝试实现一种持久化数据的方法。这两篇博文将会提供很多信息，因为它们将会是我在选择这两篇博文时的笔记。

## 我想完成的事情

*   在应用程序退出时保留数据

## 事情我办成了

*   何时以及为何使用[核心数据](https://developer.apple.com/documentation/coredata)
*   何时以及为何使用 [CloudKit](https://developer.apple.com/documentation/cloudkit)

## 我学到的东西

这一整节可能是“如何在应用程序退出时保存数据”，但我将分别讨论每一部分。

### 何时为何使用[核心数据](https://developer.apple.com/documentation/coredata)

当数据需要在本地时，使用核心数据。

苹果是这样描述核心数据的。

> 核心数据降低了创建和维护应用程序模型层的复杂性。通过使用核心数据来定义数据结构，您可以删除大多数通常需要的重复代码。

### 何时及为何使用 [CloudKit](https://developer.apple.com/documentation/cloudkit)

当需要在设备之间同步数据时，使用 CloudKit。

这是苹果对 CloudKit 的描述。

> CloudKit 框架提供了在应用程序和 iCloud 容器之间移动数据的接口。您使用 CloudKit 获取应用程序的现有数据，并将其存储在云中，以便用户可以在多个设备上访问它。您还可以将数据存储在所有用户都可以访问的公共区域。

我可以使用 CloudKit 的核心数据来同步 iCloud。这样，我可以在某个时候将它扩展到其他设备，并在它们之间共享数据。

首先，我将实现核心数据，然后在稍后添加 CloudKit。我想在小块工作，并随着应用程序的增长添加更多的功能。

我仍在学习和尝试实现核心数据，所以我还不会谈论任何编码实现。也许明天？

如果你对我做了什么或者我是如何实现的有任何疑问，请告诉我！如果你有任何建议或其他意见，也让我知道！