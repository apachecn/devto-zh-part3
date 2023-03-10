# Grails 视图-呈现空列表

> 原文：<https://dev.to/joemccall86/grails-views-rendering-empty-lists-4bhn>

你有像这样的控制器动作吗？

```
def index() {
  respond myService.listItems()
} 
```

Enter fullscreen mode Exit fullscreen mode

那个控制器会显示这样的视图吗？

```
model {
  List<Item> itemList
}

json g.render(itemList) 
```

Enter fullscreen mode Exit fullscreen mode

当 itemList 被填充时，这可能看起来工作得很好，但是您可能会注意到，当 index 方法在列表中没有项目的情况下运行时，API 被破坏:

```
$ curl http://localhost:8080/api/items
[
   null
] 
```

Enter fullscreen mode Exit fullscreen mode

什么？这应该会呈现`[]`,因为我们用一个空列表来响应。发生了什么事？

让我们从这里找到的文档来看看这个:[http://views.grails.org/latest/](http://views.grails.org/latest/)

> 如果集合为空，emptyCollection 将用作默认模型名称。这是因为无法检查第一个对象的类型。

所以我们模型上的 itemList 字段为空，因为自动模型绑定计算出 emptyCollection。这是有技术原因的。解决这个问题的最快方法是改变我们调用 respond 的方式:

```
def index() {
  respond([itemList: myService.listItems()])
} 
```

Enter fullscreen mode Exit fullscreen mode

这将强制手动绑定模型中的 itemList，无论它是否为空。