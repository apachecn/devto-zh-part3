# 角度 PWAs 入门-第 2 部分

> 原文：<https://dev.to/prestonjlamb/getting-started-with-angular-pwas-part-2-poe>

在这个渐进式 Web 应用程序系列的第一部分中，我们讨论了如何使用 Angular 创建 PWA，以及如何开始设置服务工作者来缓存文件和 API 请求。如果你没有读过它，也没有使用 Angular PWAs 的经验，你可能想在继续第二部分之前读一下。

## 通知更新

好的，让我们首先通知应用程序的用户，他们有一个更新。我们在上一篇文章中看到，更新是在后台完成的，然后在刷新后使用新的应用程序文件。但是用户怎么知道有更新，他们应该刷新应用程序呢？好吧，Angular 又一次让这变得很容易。

让我们首先创建一个新的组件来处理这个问题:

```
ng g c update-notification 
```

Enter fullscreen mode Exit fullscreen mode

在该应用程序中，从@angular/service-worker:
注入 SwUpdate 服务

```
constructor(private _update: SwUpdate) {} 
```

Enter fullscreen mode Exit fullscreen mode

和 ngOnInit 函数:

```
ngOnInit() {
  if (this._update.isEnabled) {
    this._update.available.subscribe(() => {
      this.updateAvailable = true;
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`ngOnInit`函数中的这段代码检查我们注入的 SwUpdate 服务，看是否启用了更新，如果启用了，它就订阅那个可观察的。当发出一个新值时，我们将局部变量`this.updateAvailable`设置为 true。然后，我们可以在 UI 中显示一些内容，允许用户选择是否更新应用程序。用户界面可以是这样的:

```
<p *ngIf="updateAvailable" class="update-notification">
    There is a new version of this app available. <span (click)="doUpdate()">Update now?</span>
</p> 
```

Enter fullscreen mode Exit fullscreen mode

`doUpdate()`功能很简单:

```
doUpdate() {
  this.updateAvailable = false;
  window.location.reload();
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是向用户显示更新或不更新选项的全部内容。从技术上来说，你可以在不给他们选择的情况下进行更新，但也许他们的网络很慢，所以下载新文件需要几秒钟，同时他们已经填写了某种形式的表格。自动更新会导致他们丢失输入的数据。这样他们可以选择何时更新。

## 安装提示

另一项对用户和渐进式 web 应用程序交互有益的技术是安装提示。我相信 Chrome 和大多数浏览器都会默认显示一个提示，询问用户是否愿意在他们的设备上安装该应用程序。但是他们可以拒绝该提示，如果他们拒绝，可能需要一段时间，直到该提示再次出现。在一个地方，我读到 Chrome 可能需要几个月才会再次询问。我们可能不想在询问之前等待那么长时间，所以有一种方法可以提示用户。下面的 JavaScript 代码会帮你解决这个问题:

```
var promptEvent;
var btn = document.querySelector('#pwa-install-button');

window.addEventListener('beforeinstallprompt', event => {
  promptEvent = event;
  btn.style.display = 'block';
  console.log('beforeinstallprompt caught');
});

btn.addEventListener('click', () => {
  if (promptEvent) {
    promptEvent.prompt();
    promptEvent.userChoice.then(result => {
      if (result.outcome === 'accepted') {
        console.log('user accepted add to homescreen');
      } else {
        console.log('user dismissed the add to homescreen');
      }
      promptEvent = undefined;
    });
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看这里发生了什么。我们首先监听窗口上的`beforeinstallprompt`事件，只有当应用程序尚未安装并且满足 PWA 要求时，该事件才会触发。当它发生时，我们将提示保存在一个局部变量中并显示一个按钮。然后，我们向按钮添加一个事件侦听器，并等待它被单击。当它存在时，我们在`promptEvent`上调用`prompt()`方法。我们等待结果，然后将`promptEvent`设置为 undefined，这样按钮再次隐藏。事件本身处理提示的显示和应用程序的实际安装。

你可以把上面的代码放在 Angular 应用的某个地方，但是根据我的阅读和测试，那个`beforeinstallprompt`在窗口加载后很快就触发了，我的 Angular 应用没有捕捉到这个事件。因此，我将它放在 assets 文件夹中的一个普通 JS 文件中，并手动将其加载到 index.html 文件中。我还将按钮直接放入 HTML 文件中，然后在应用程序的全局 CSS 文件中对其进行样式化。

关于上面的代码有一点要注意:我还没有让它在 Android 的 Firefox 上运行。Android 版 Firefox 中有一个“添加到主屏幕”按钮，但是“安装”按钮从未显示，所以在我看来,`beforeinstallprompt`事件从未触发。该事件确实在 Android 版 Chrome 上引发了争议。所以要意识到这一点。

## 结论

这是我到目前为止学到的两种技术，可以让 PWAs 的工作和用户体验变得更好。我希望(当然我会)在接下来的几个月里学习更多的技术，因为我在为工作做 PWA 项目。随着我对它了解的越来越多，我将继续这个系列，为大家添加更多的信息和更多的技术。