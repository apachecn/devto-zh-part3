# 第 005 天:制作待办事项清单

> 原文：<https://dev.to/maeganwilson_/day-005-making-a-todo-list-3dcb>

[GitHub 回购](https://github.com/maeganjwilson/todo_list)

## 我想完成的事情:

*   用勾号标记已完成的待办事项
*   将已完成的任务排在列表的底部

## 事情我办成了

*   用勾号标记已完成的待办事项
*   将已完成的任务排在列表的底部

## 我学到的东西

### 如果要选择行，确保有 UITableViewDelegate

没有`UITableViewDelegate`让我无法选择和取消选择细胞。我也花了一点时间来排除故障，因为乍一看我是在做必要的事情。在其他几篇栈溢出文章之后，我意识到我没有将`UITableViewDelegate`赋值给 self。下面是选择工作所必需的最后代码:

```
// UITableViewDelegate here
class ViewController: UIViewController, UITextFieldDelegate, UITableViewDelegate, UITableViewDataSource {

    @IBOutlet weak var todoTable: UITableView!

    var todos = TodoList()

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
        todoInput.delegate = self
        todoInput.returnKeyType = .done

        //table view setup
        todoTable.dataSource = self
        todoTable.delegate = self // <—— this line
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 如何在 Swift 中通过项目进行枚举

为了实现我的排序，我需要能够遍历数组中的每个 todo，并检查它是否完整。我选择使用`enumerated()`，因为它将返回 todo 的索引和实际的 todo 本身。

```
for (i, todo) in todoList.enumerated(){
            if (todo.complete) {
                let element = todoList.remove(at: i)
                todoList.insert(element, at: todoList.count)
            }
        } 
```

Enter fullscreen mode Exit fullscreen mode

如果你对我做了什么或者我是如何实现的有任何疑问，请告诉我！如果你有任何建议或其他意见，也让我知道！