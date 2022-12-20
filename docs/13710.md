# 第 011 天:制作待办事项清单

> 原文：<https://dev.to/maeganwilson_/day-011-making-a-todo-list-2928>

[GitHub 回购](https://github.com/maeganjwilson/todo_list)

第 10 天，我学习了如何从头实现核心数据的教程。今天，我将尝试把它实现到我目前为止一直在做的现有应用程序中。

## 我想完成的事情

*   在应用程序退出时保留数据

## 事情我办成了

*   向应用添加核心数据

## 我学到的东西

### 核心数据在项目开始时更容易添加

创建 iOS 项目时，有一个选项可以将核心数据添加到项目中。在开始这个项目时，我选择了“否”,因为我不想让项目过于复杂。通过在开头添加它，`AppDelegate.swift`文件将为我添加以下内容:

```
// MARK: - Core Data stack
    lazy var persistentContainer: NSPersistentContainer = {
        /*
         The persistent container for the application. This implementation
         creates and returns a container, having loaded the store for the
         application to it. This property is optional since there are legitimate
         error conditions that could cause the creation of the store to fail.
         */
        let container = NSPersistentContainer(name: "TodoList")
        container.loadPersistentStores(completionHandler: { (storeDescription, error) in
            if let error = error as NSError? {
                // Replace this implementation with code to handle the error appropriately.
                // fatalError() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.

                /*
                 Typical reasons for an error here include:
                 * The parent directory does not exist, cannot be created, or disallows writing.
                 * The persistent store is not accessible, due to permissions or data protection when the device is locked.
                 * The device is out of space.
                 * The store could not be migrated to the current model version.
                 Check the error message to determine what the actual problem was.
                 */
                fatalError("Unresolved error \(error), \(error.userInfo)")
            }
        })
        return container
    }()

    // MARK: - Core Data Saving support

    func saveContext () {
        let context = persistentContainer.viewContext
        if context.hasChanges {
            do {
                try context.save()
            } catch {
                // Replace this implementation with code to handle the error appropriately.
                // fatalError() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                let nserror = error as NSError
                fatalError("Unresolved error \(nserror), \(nserror.userInfo)")
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这些允许在应用程序中使用`TodoList.xcdatamodel`,并在核心数据中保存信息。

由于最初制作 app 时没有添加核心数据，所以不得不手动将变量和函数添加到`AppDelegate.swift`文件中。为了弄清楚那里有什么，我用核心数据创建了一个新的应用程序，并将它们复制/粘贴到我当前的 Xcode 项目中。

* * *

如果你对我做了什么或者我是如何实现的有任何疑问，请告诉我！如果你有任何建议或其他意见，也让我知道！