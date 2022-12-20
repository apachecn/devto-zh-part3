# 如何使用 JavaScript 显示桌面通知

> 原文：<https://dev.to/attacomsian/how-to-show-desktop-notifications-using-javascript-5aco>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/desktop-notifications-javascript)。*

* * *

JavaScript Notifications API 使网页能够跨不同平台在用户的设备上向用户显示消息。即使在用户切换标签或移动到另一个应用程序后，这些通知也会出现。

这些消息(也称为**系统**或**桌面**通知)可用于通知用户重要事件，如电子邮件、新的社交媒体消息、实时聊天通知、日历提醒等。您甚至可以使用系统通知来发送营销活动。

在本教程中，我将解释在浏览器标签中打开站点时，通知 API 向用户显示消息的基本用法。

## API 用法

通知 API 相当新，可能无法在较旧的浏览器上工作。因此，在显示消息之前，您应该明确验证浏览器是否支持该 API。你可以通过检查`window`对象是否有一个名为`Notification` :
的属性来做到这一点

```
if(!window.Notification) {
    console.log('Browser does not support notifications.');
} else {
    // display message here
} 
```

Enter fullscreen mode Exit fullscreen mode

在受支持的平台上，显示桌面通知涉及两件事:请求权限和创建新通知以显示给用户。

> **注意:**通知 API 要求您的网站使用 SSL 证书。它不适用于不安全的源(HTTP)。

## 请求许可

用户需要授予当前源权限来显示桌面通知。您可以使用`Notification.permission`属性轻松检查用户是否已经被授予显示系统通知的权限。该属性具有以下可能值:

*   用户尚未决定接受来自你网站的通知
*   `granted` -用户允许您的网站显示通知
*   `denied` -用户选择阻止来自您网站的通知

如果是第一种情况，您可以通过使用通知 API 的`requestPermission()`方法向用户请求权限。它将打开一个对话框，询问用户是允许还是阻止来自该站点的通知。一旦用户做出选择，设置将被保存用于当前会话。

如果用户已经拒绝了显示通知的请求，我们就不能做任何事情。浏览器将忽略任何显示通知或再次请求权限的请求。

```
if (!window.Notification) {
    console.log('Browser does not support notifications.');
} else {
    // check if permission is already granted
    if (Notification.permission === 'granted') {
        // show notification here
    } else {
        // request permission from user
        Notification.requestPermission().then(function(p) {
           if(p === 'granted') {
               // show notification here
           } else {
               console.log('User blocked notifications.');
           }
        }).catch(function(err) {
            console.error(err);
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`requestPermission()`方法返回一个[承诺](https://attacomsian.com/blog/promises-javascript)。一旦承诺被解决或拒绝(根据用户对通知的选择)，回调函数将被调用。

## 显示通知

如果用户选择了接受来自我们站点的通知，您可以使用`Notification()`构造函数创建一个新的桌面通知来显示给用户。只需向构造函数传递一个标题来创建一个新的通知。

```
var notify = new Notification('Hi there!'); 
```

Enter fullscreen mode Exit fullscreen mode

或者，您也可以传递一个 options 对象来指定文本方向、正文、要显示的图标、要播放的通知声音等等。

```
var notify = new Notification('Hi there!', {
    body: 'How are you doing?',
    icon: 'https://bit.ly/2DYqRrh',
}); 
```

Enter fullscreen mode Exit fullscreen mode

把所有的东西放在一起，我们可以创建一个函数，一旦被调用，它将显示一个桌面通知，请求许可，如果还没有被授予的话:

```
function notifyMe() {
    if (!window.Notification) {
        console.log('Browser does not support notifications.');
    } else {
        // check if permission is already granted
        if (Notification.permission === 'granted') {
            // show notification here
            var notify = new Notification('Hi there!', {
                body: 'How are you doing?',
                icon: 'https://bit.ly/2DYqRrh',
            });
        } else {
            // request permission from user
            Notification.requestPermission().then(function (p) {
                if (p === 'granted') {
                    // show notification here
                    var notify = new Notification('Hi there!', {
                        body: 'How are you doing?',
                        icon: 'https://bit.ly/2DYqRrh',
                    });
                } else {
                    console.log('User blocked notifications.');
                }
            }).catch(function (err) {
                console.error(err);
            });
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在用户点击一个按钮时调用上面的函数:

```
<button onclick="notifyMe()">Notify Me</button> 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以将上述函数绑定到 body `onload`事件，该事件将在网页完全加载后被调用:

```
<!DOCTYPE html>
<html>
<body onload="notifyMe()">
<!-- body content-->
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。