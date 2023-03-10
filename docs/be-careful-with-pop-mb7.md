# 小心……流行音乐

> 原文：<https://dev.to/defman/be-careful-with-pop-mb7>

我一直在与我的应用程序中的一个奇怪的错误作斗争:如果我向 2+个用户发送推送通知，只有第一个用户会收到通知。

问题是我从数据中删除了键。

下面是它的工作原理(伪代码):

```
def send(users: [User], data: dict):
    notifications = reduce(lambda acc, user: acc + [Notification(user, data)], users, [])
    db.bulk_insert(notifications)
    for notification in notifications:
        notification.send()

...

def Notification.send(self):
    message = self.data.pop('message') 
    Firebase.send(message, self.data) 
```

Enter fullscreen mode Exit fullscreen mode

第一个通知将被发送，我原以为`message`会从`self.data`中弹出，但实际上**会从`self.data`指向的字典中弹出！**这意味着在`send`函数中使用`Notification(user, data)`(而不是`Notification(user, data.copy())`)初始化的每个通知对象将不再有`data['message']`，导致意外的行为。

如果你真的需要删除字典中的一个键，你应该在变异前删除它。
如果你没有——就坚持`.get()`。