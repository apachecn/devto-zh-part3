# 对 Angular 的非政府组织的深入研究

> 原文：<https://dev.to/angular/making-upgrades-to-angular-ngondestroy-376a>

这篇文章是我本周早些时候发出的一条有角度的热门提示推特的延续。它变得非常受欢迎，并引起了广泛的讨论。本文中探讨的概念反映了这一讨论，因此您可能需要花些时间来看看下面的内容:

液体错误:内部

作为上述 tweet 的延伸，我们将讨论如何以及何时调用`ngOnDestroy`的限制。我们还将讨论克服这些限制的方法。如果你是 Angular 的新手，或者是 Angular 生命周期方法的新手，那么我建议你在这里查阅[官方文档。](https://angular.io/guide/lifecycle-hooks)

* * *

## NPM 包版本

对于上下文，本文假设您正在使用以下`npm` `package.json`版本:

*   `@angular/*` : 7.2.9

* * *

## NGO ndestroy 简介

在我们深入探讨之前，让我们花几分钟时间回顾一下`ngOnDestroy`。

NgOnDestroy 是一个生命周期方法，可以通过在类上实现`OnDestroy`并添加一个名为`ngOnDestroy`的新类方法来添加。根据 [Angular 文档](https://angular.io/guide/lifecycle-hooks#lifecycle-sequence)，它的主要目的是“在 Angular 破坏指令/组件之前进行清理。取消订阅观察对象并分离事件处理程序以避免内存泄漏。在 Angular 销毁指令/组件之前调用。

### 一个泄漏的 MyValueComponent

假设我们有一个名为`MyValueComponent`的组件，它在`ngOnInit`方法中从`MyService`订阅一个值:

```
import { Component, OnInit } from '@angular/core';
import { MyService } from './my.service';

@Component({
  selector: 'app-my-value',
  templateUrl: './my-value.component.html',
  styleUrls: [ './my-value.component.css' ]
})
export class MyValueComponent implements OnInit {
  myValue: string;

  constructor(private myService: MyService) {}

  ngOnInit() {
      this.myService.getValue().subscribe(value => this.myValue = value);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果这个组件在一个 Angular 应用程序的生命周期中被多次创建和销毁，那么每次它被创建时,`ngOnInit`将被称为创建一个全新的订阅。这可能会很快失去控制，我们的价值将呈指数级更新。这就是所谓的“内存泄漏”。内存泄漏会对应用程序的性能造成严重破坏，此外还会增加不可预测或意想不到的行为。让我们继续读下去，学习如何堵住这个漏洞。

### 修复 MyValueComponent 上的漏洞

为了修复内存泄漏，我们需要用订阅中的`OnDestroy`和`unsubscribe`的实现来扩充组件类。让我们更新我们的组件，添加以下内容:

*   将`OnDestroy`添加到打字稿`import`
*   将`OnDestroy`添加到`implements`列表中
*   创建一个名为`myValueSub: Subscription`的类字段来跟踪我们的订阅
*   设置`this.myValueSub`等于`this.myService.getValue().subscription`的值
*   创建一个名为`ngOnDestroy`的新类方法
*   如果已经设置了订阅，请在`ngOnDestroy`内调用`this.myValueSub.unsubscribe()`。

更新后的组件将如下所示:

```
import { Component, OnInit, OnDestroy } from '@angular/core';
import { MyService } from './my.service';

@Component({
  selector: 'app-my-value',
  templateUrl: './my-value.component.html',
  styleUrls: [ './my-value.component.css' ]
})
export class MyValueComponent implements OnInit, OnDestroy {
  myValue: string;
  myValueSub: Subscription;

  constructor(private myService: MyService) {}

  ngOnInit() {
      this.myValueSub = this.myService.getValue().subscribe(value => this.myValue = value);
  }

  ngOnDestroy() {
    if (this.myValueSub) {
        this.myValueSub.unsubscribe();
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 超越内存泄漏

太好了！现在您已经有了一些关于`ngOnDestroy`的背景知识，以及清理内存泄漏是这种生命周期方法的主要用例。但是如果您想更进一步，添加额外的清理逻辑呢？如何进行服务器端清理调用？也许阻止用户导航离开？

当你继续阅读时，我们将讨论三种方法来升级你的`ngOnDestroy`以达到最佳使用效果。

* * *

## 升级#1 -使 NgOnDestroy 异步

与 Angular 中的其他生命周期方法一样，可以用`async`修改`ngOnDestroy`。这将允许您调用返回`Promise`的方法。这可能是管理应用程序中清理活动的一种强有力的方法。当你继续阅读的时候，我们将会探究一个这样的例子。

### 添加逻辑从 ngOnDestroy 调用 AuthService.logout

让我们假设当`MyValueComponent`被销毁时，您需要执行服务器端注销。为此，我们将如下更新该方法:

*   将`AuthService`添加到您的`imports`中
*   将`AuthService`添加到您的`constructor`中
*   在方法名`ngOnDestroy`前添加`async`
*   使用`await`关键字调用`AuthService`到`logout`。

您更新后的`MyValueComponent`看起来会像这样:

```
import { Component, OnInit, OnDestroy } from '@angular/core';
import { MyService } from './my.service';
import { AuthService } from './auth.service';

@Component({
  selector: 'app-my-value',
  templateUrl: './my-value.component.html',
  styleUrls: [ './my-value.component.css' ]
})
export class MyValueComponent implements OnInit, OnDestroy {
  myValue: string;
  myValueSub: Subscription;

  constructor(private myService: MyService, private authService: AuthService) {}

  ngOnInit() {
      this.myValueSub = this.myService.getValue().subscribe(value => this.myValue = value);
  }

  async ngOnDestroy() {
    if (this.myValueSub) {
        this.myValueSub.unsubscribe();
    }

    await this.authService.logout();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

Tada！现在，当组件被销毁时，将发出一个`async`调用来注销用户并销毁他们在服务器上的会话。

## 升级#2 -确保浏览器事件期间的执行

许多开发人员惊讶地发现,`ngOnDestroy`只有在实现它的类在运行的浏览器会话环境中被破坏时才会被触发。

换句话说，在以下场景中，`ngOnDestroy`被*而不是*可靠地调用:

*   页面刷新
*   标签关闭
*   浏览器关闭
*   离开页面导航

考虑到之前让用户在销毁时注销的例子，这可能会成为一个障碍。为什么？嗯，大多数用户会简单地关闭浏览器会话或导航到另一个网站。那么，如果`ngOnDestroy`在这些场景中不起作用，我们如何确保捕捉或挂钩到该活动呢？

### 用 HostListener 装饰 ngOnDestroy

> TypeScript decorators 在整个 angle 应用程序中都有使用。更多信息可以在[官方打字稿文档](https://www.typescriptlang.org/docs/handbook/decorators.html)中找到。

为了确保我们的`ngOnDestroy`在上面提到的浏览器事件中执行，我们可以在`ngOnDestroy`的顶部添加一行简单的代码。让我们继续我们之前的`MyValueComponent`的例子，并装饰`ngOnDestroy`:

*   将`HostListener`添加到`imports`中
*   将`@HostListener('window:beforeunload')`放在`ngOnDestroy`的顶部

我们更新后的`MyValueComponent`看起来会像这样:

```
import { Component, OnInit, OnDestroy, HostListener } from '@angular/core';
import { MyService } from './my.service';
import { AuthService } from './auth.service';

@Component({
  selector: 'app-my-value',
  templateUrl: './my-value.component.html',
  styleUrls: [ './my-value.component.css' ]
})
export class MyValueComponent implements OnInit, OnDestroy {
  myValue: string;
  myValueSub: Subscription;

  constructor(private myService: MyService, private authService: AuthService) {}

  ngOnInit() {
      this.myValueSub = this.myService.getValue().subscribe(value => this.myValue = value);
  }

  @HostListener('window:beforeunload')
  async ngOnDestroy() {
    if (this.myValueSub) {
        this.myValueSub.unsubscribe();
    }

    await this.authService.logout();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的`ngOnDestroy`方法在组件被 Angular 破坏和浏览器事件`window:beforeunload`被触发时都会被调用。这是一个强大的组合！

### 关于 HostListener 的更多信息

是一个有角度的装饰器，可以放在任何类方法之上。这个装饰器有两个参数:`eventName`和可选的`args`。在上面的例子中，我们将`window:beforeunload`作为 DOM 事件传递。这意味着 Angular 将在 DOM 事件`window:beforeunload`被触发时自动调用我们的方法。关于`@HostListener`的更多信息，请查看[官方文档](https://angular.io/api/core/HostListener)。

如果您想使用它来防止导航离开页面或组件，那么:

*   将`$event`添加到`@HostListener`参数中
*   呼叫`event.preventDefault()`
*   将`event.returnValue`设置为您希望浏览器显示的消息的字符串值

一个例子应该是这样的:

```
@HostListener('window:beforeunload', ['$event'])
async ngOnDestroy($event) {
  if (this.myValueSub) {
    this.myValueSub.unsubscribe();
  }

  await this.authService.logout();

  $event.preventDefault();
  $event.returnValue = 'A message.';
} 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意:这不是 Angular 官方支持的！`OnDestroy`和`ngOnDestroy`表示不允许在`ngOnDestroy`上输入参数。虽然不受支持，但它实际上仍能正常工作。

### 关于窗口的更多信息:卸载前

`window:beforeunload`是在`window`卸载之前触发的事件。更多细节可以在这里的文档中找到:[https://developer . Mozilla . org/en-US/docs/Web/API/Window/before unload _ event](https://developer.mozilla.org/en-US/docs/Web/API/Window/beforeunload_event)。

有几点需要注意:

*   除 iOS Safari 之外，目前所有主流浏览器都支持此事件。

*   如果你在 iOS Safari 中需要这个功能，那么考虑查看这个[堆栈溢出线程](https://stackoverflow.com/questions/14645011/window-onbeforeunload-and-window-onunload-is-not-working-in-firefox-safari-o/14647730#14647730)。

*   如果您使用此事件试图阻止导航离开，您必须将`event.returnValue`设置为您想要显示的消息字符串。在这个[例子](https://developer.mozilla.org/en-US/docs/Web/API/Window/beforeunload_event#Examples)中有更多的细节。

## 结论

我意识到本文中推荐的一些技巧不是主流，可能会引起一些关注。请记住一如既往地尝试这些方法，看看它们是否适合您在应用程序中所做的事情。如果他们工作出色！如果没有，那就继续前进吧。

如果您有任何意见或问题，请随时通过 [Twitter](https://twitter.com/wesgrimes) 联系我

* * *

## 附加资源

我强烈推荐参加终极角度课程。它物有所值，我已经把它作为新手和有经验的 Angular 开发者的培训工具。点击下面的链接注册。

[终极课程:JavaScript、Angular、NGRX 和 TypeScript 专家在线课程](https://bit.ly/2WubqhW)