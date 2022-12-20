# 第 008 天:制作待办事项清单

> 原文：<https://dev.to/maeganwilson_/day-009-making-a-todo-list-4bka>

[GitHub 回购](https://github.com/maeganjwilson/todo_list)

**问题**:第 6 天和第 7 天在哪里？

**回答**:因为是
周末，我没做什么编码，很忙。

## 我想完成的事情

*   要删除的幻灯片

## 事情我办成了

*   要删除的幻灯片

## 我学到的东西

### 该功能需要添加“幻灯片删除”。

需要的功能是这个

```
func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
        // CODE GOES HERE
    } 
```

我选择实现该函数的方式是检查
`editingStyle`是否等于`.delete`，在该检查中，我执行以下操作:

*   删除待办事项
*   从表格视图中删除该行

下面是最终代码

```
func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
        if editingStyle == .delete {
            todos.deleteTodo(index: indexPath.row)
        }
        tableView.deleteRows(at: [indexPath], with: .left)
    } 
```

如果你对我做了什么或者我是如何实现的有任何疑问，请告诉我！如果你有任何建议或其他意见，也让我知道！