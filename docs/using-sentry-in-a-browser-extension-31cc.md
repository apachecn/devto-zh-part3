# 在浏览器扩展中使用 Sentry

> 原文：<https://dev.to/mgrandera/using-sentry-in-a-browser-extension-31cc>

你可能认为利用 Sentry 跟踪浏览器扩展中的错误很容易，但实际上并不容易。我就是这么解决的。

# 安装岗哨

要在浏览器扩展中使用 sentry，首先使用 yarn 或 npm 安装包

```
# npm
npm install @sentry/browser
# yarn
yarn add @sentry/browser 
```

# 利用哨兵

要使用 sentry，你必须遵循他们在网站上给你的常规设置

```
import * as sentry from "@sentry/browser";

Sentry.init({
  dsn: "YOUR_DSN"
})

function example() {
  throw new Error("Example Error")
}
example() 
```

现在，您可能认为您将跟踪错误，但不幸的是，这将无法开箱即用。首先，您必须对上面的代码进行一些修改:

1.  用 try-catch 封装整个执行过程
2.  为“unhandledrejection”事件添加 eventlistener

代码将如下所示:

```
import * as sentry from "@sentry/browser";

Sentry.init({
  dsn: "YOUR_DSN"
})

window.addEventListener('unhandledrejection', event => {
  Sentry.captureEvent(event.reason)
});

function example() {
  throw new Error("Example Error")
}

try {
  example()
} catch (e) {
  Sentry.captureEvent(e)
} 
```

**成功**现在，sentry 将捕获错误/异常，但您会很快注意到一些看起来不太正常的事情。

# 修复问题

如果你上传 sourcemaps 到 sentry，你会期望它们被用来简化错误的查找，但是不幸的是，默认情况下，sentry 在用户系统的完整路径下在`.js`文件中报告错误。您可能会看到如下路径的错误描述:

```
.../Default/Extensions/<extension-id>/0.0.1.10_0/content_script.js 
```

而不仅仅是截断的相对路径。

我们必须在发送事件之前手动截断路径，以确保 sentry 能够识别源地图的文件。我们通过在`Sentry.init`调用中指定一个`beforeSend`函数来做到这一点。

```
Sentry.init({
  dsn: 'YOUR_DSN',
  beforeSend(event, hint) {
    if (event.exception) {
      event.exception.values[0].stacktrace.frames.forEach((frame) => {
        frame.filename = frame.filename.substring(frame.filename.lastIndexOf("/"))
      });
    }
    return event;
  } 
}); 
```

这将正确地报告错误，并且 sentry 将使用正确的源地图。

##### 我用这个做什么？

我目前正在开发 StreamParty，这是一个浏览器扩展，可以让你在多个平台上与你的另一半同步观看视频流。看看吧！[https://www.streamparty.tv/](https://www.streamparty.tv/)