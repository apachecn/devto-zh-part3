# 第 002 天:制作待办事项清单

> 原文：<https://dev.to/maeganwilson_/day-002-making-a-todo-list--3jgm>

[点击此处获取代码！](https://github.com/maeganjwilson/todo_list) `// Link goes to GitHub.`

## 我想完成的目标

1.  使`UITextField`工作
2.  让按钮工作
3.  填充表格视图

## 我完成的事情:

1.  输入任务的能力(以上第 1 条)
2.  将任务添加到待办事项列表(上面的第 2 条)
3.  用按钮添加 todo 后关闭键盘。(1 号和 2 号)
    1.  如果使用 Done 键，键盘不会消失
4.  填充表格视图(上面的数字 3)

## 我学到的东西

### 为 todo 列表创建一个单独的类让事情变得有点困难。

```
struct Todo {
    var task: String
    var complete:Bool = false
}

class TodoList {
    private var todoList = [Todo]()

    func addTodo(todo: String) {
        let newTodo = Todo(task: todo, complete: false)
        todoList.append(newTodo)
    }

    func returnList() -> Array<Todo> {
        return todoList
    }

    func count() -> Int {
        return todoList.count
    }

    func getTodo(index: Int) -> Todo {
        return todoList[index]
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果我需要获取特定索引处的 Todo，我必须为它编写自己的函数。这同样适用于获取待办事项列表中待办事项的数量。

我使用了类和结构来帮助组织，并且将我的数据保存在我的`ViewController.swift`文件之外。

### 将表格链接到视图控制器

我遇到的问题是将表格视图链接到视图控制器。我找到了一些在线教程来帮助我，但没有一个解释如何连接这两者。我现在就解释。

按住 option，从表格视图中单击并拖动到`ViewController`类中的`ViewController.swift`，制作一个`IBOutlet`。
下面是我的代码行:

```
@IBOutlet weak var todoTable: UITableView! 
```

Enter fullscreen mode Exit fullscreen mode

如果你对我做了什么或者我是如何实现的有任何疑问，请告诉我！如果你有任何建议或其他意见，也让我知道！