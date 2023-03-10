# 通知 API 简介

> 原文：<https://dev.to/ruheni/introduction-to-the-notification-api-4687>

通知 API 使网站能够在用户浏览器之外发送通知，如系统通知。这允许网站或 webapp 在后台向用户发送信息。

我们将浏览一下如何设置和开始使用这个 API 的基础知识。

这篇文章是对初学者友好的，只有基本的编程知识就足够了。

# 第一步:检查当前权限状态。

您可以使用 Notification.permission 在浏览器控制台上检查权限的状态。它有三个可能的值:“默认”、“拒绝”和“授予”。

```
Notification.permission 
```

Enter fullscreen mode Exit fullscreen mode

# 第二步:授予权限。

如果网站或 webapp 未被授予开始显示通知的权限

```
Notification.requestPermission().then(function(res) {
  console.log(res);
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 第三步:新建通知。

一旦授予用户权限，您就可以使用构造函数来初始化一个新的通知，并将一个字符串作为参数传递到一个`Notification`构造函数中，即

```
let notification = new Notification('Hello there'); 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经知道了如何向用户请求权限，我们可以稍微清理一下，并创建一个名为`notifyMe()`的函数，它将包含检查权限的代码，并从网站或 web 应用程序发送通知。

```
function notifyMe() {
    let img = 'https://img.icons8.com/color/48/000000/task.png';

    // check for browser support
    if (!('Notification' in window)) {
        alert('This browser does not support system notifications.');
    }
    // check whether notification permissions have already been granted
    else if (Notification.permission === 'granted') {
        // if ok, create notification
        let text = `Hey there, pick up groceries is now overdue`;

        let notification = new Notification('To Do list', {
            body: text,
            icon: img
        });
    }
    // otherwise, ask user for permission
    else if (
        Notification.permission === 'denied' ||
        Notification.permission === 'default'
    ) {
        Notification.requestPermission(permission => {
            // if user accepts, let's create notification
            if (permission === 'granted') {
                let notification = new Notification('To Do list', {
                    body: text,
                    icon: img
                });
            }
        });
    }
}

// call the function
notifyMe(); 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经注意到，`Notification`构造函数能够接受第二个参数`options`，它是一个包含细节的对象，比如在通知正文中使用的图标和文本，以增强内容。

使用的图像是一个剪贴板来表示给定的任务。你可以在这里查看

下面是 Github repo 的链接，包含本文中使用的代码:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [鲁赫尼](https://github.com/ruheni) / [沙盒](https://github.com/ruheni/sandbox)

### 这个存储库包含用于学习各种编程概念的文件

<article class="markdown-body entry-content container-lg" itemprop="text">

# 沙箱

这个库包含用于学习我正在学习的各种编程语言中的各种编程概念的文件

**项目**

[**1。**通报](https://github.com/ruheni/sandbox/tree/master/notification)

在这个文件夹中，我探索了用于请求用户许可并从 web 应用程序或网站向用户发送通知的`Notification`浏览器 API。它们在后台向用户发送通知。不使用外部 API 向用户发送通知。

[**2。Web-Components**](https://github.com/ruheni/sandbox/tree/master/web-components)

我探索了如何在 JavaScript 中制作`custom HTML elements`,如何使用`Shadow DOM`,以及如何使用类来声明元素并导出它们以呈现在 HTML 页面上。`news-article.js`是包含在`index.js`文件中创建和使用的`custom HTML element`的文件，最终解析 JSON 并在`index.html`上呈现它们。

[**3。测试**T3】](https://github.com/ruheni/sandbox/tree/master/testing)

`Mocha`用于在 JavaScript 中编写和运行`unit tests`。我用它来学习如何写测试…

</article>

[View on GitHub](https://github.com/ruheni/sandbox)

*这是我的第一篇技术文章，如果你对如何提高我的写作有任何建议，或者你希望我写什么样的内容，请随时直接给我发消息*🙂 🙃