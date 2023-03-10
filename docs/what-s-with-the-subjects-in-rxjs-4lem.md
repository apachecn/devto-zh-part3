# RxJS 中的主题是什么

> 原文：<https://dev.to/samvloeberghs/what-s-with-the-subjects-in-rxjs-4lem>

*最初发布于[https://samvloeberghs . be](https://samvloeberghs.be/posts/whats-with-the-subjects-in-rxjs5)2016 年 7 月 14 日*

RxJS 是一个很棒的库，它越来越多地被用在不同的地方&网络框架中。很快，它将主导 web 应用程序的异步部分，所以这就是为什么了解主题如何工作很重要。结合了安`Observable`和`Observer`的优点。所以你可以认为一个`Subject`同时是一个`Observable`和`Observer`。

一个`Subject`是一个`Observable`，但是一个`Observable`是单播。`Subjects`另一方面是多播，这意味着它们像`EventEmitters`一样可以处理一组多个侦听器或订户。为了促进这种行为，新的观察者被登记在一个`Observers`列表中，所有的观察者都会得到通知。订阅者不知道是什么在发送更新，因为它可以是普通的`Observable`或发出值的`Subject`。

一个`Subject`是一个`Observer`，这意味着它也可以在`next(val)`上发送新的更新，在`error(err)`和`complete()`上发出错误。

有不同的口味，像普通的 T1、T2、T3 和 T4。我们将尝试解释它们之间的区别，并用一个真实的、可读的例子来支持这种解释。

## 什么是`Subject`？

一个`Subject`可以用来订阅那个`Subject`的所有观察者，也是一个`Observable`。`Subject`可以订阅另一个异步操作或源，如 API 后端，充当订阅者集合和源的代理。

### 举例

在下面的例子中，我们有两个`Observers`连接到一个`Subject`，我们向`Subject`提供一些值。请注意，订阅有不同的方式。您可以使用为下一个值定义函数的对象，或者对`next`、`error`和`complete` :
使用 3 个不同的回调函数

```
import { Subject } from 'rxjs/Subject';
let subject = new Subject<number>();

subject.subscribe(
   (val: number) => { console.log('observer I: ' + val) },
   () => {},
   () => {}
);
subject.subscribe({
    next: (val: number) => console.log('observer II: ' + val)
});

subject.next('test')  // ! compile time error !

subject.next(11);
// log: observer I: 11
// log: observer II: 11

subject.next(22);
// log: observer I: 22
// log: observer II: 22 
```

我们在这个例子中使用了 TypeScript，所以我们可以向`Subject`提供一个类型。这样，如果我们想发送下一个不符合类型的值，我们就可以在编译时进行类型检查！

请看这个例子:[https://plnkr.co/edit/nrXf41PNgUdJTMCjR9gG?p=preview](https://plnkr.co/edit/nrXf41PNgUdJTMCjR9gG?p=preview)

## 什么是`BehaviorSubject`？

`BehaviorSubject`与普通`Subject`的不同之处在于，它存储最新发出的值，并需要初始值。当一个新订户订阅了`BehaviorSubject`时，最新的值会立即发送给新订户。如果还没有发出任何值，将返回默认值。一旦`BehaviorSubject`完成，它将释放内存。新订阅的`Observers`不会收到最新的值。

### 举例

我们给`BehaviorSubject`一个初始值 11。订阅`Subject`的第一个`Observer`立即得到初始值的通知。接下来的`Observer`也是如此。

```
import { BehaviorSubject } from 'rxjs/BehaviorSubject';
let behaviorSubject = new BehaviorSubject<number>(11);

behaviorSubject.subscribe(
   (val: number) => console.log('observer I: ' + val)
);
// log: observer I: 11

behaviorSubject.subscribe({
    next: (val: number) => console.log('observer II: ' + val)
});
// log: observer II: 11 
```

从这一点上看`BehaviorSubject`的行为与正常的`Subject`相同。但是，如果我们给`BehaviorSubject`订阅一个新的`Observer`，它将立即接收最后发出的值！

```
behaviorSubject.next(22);
// log: observer I: 22
// log: observer II: 22

behaviorSubject.next(33);
// log: observer I: 33
// log: observer II: 33

behaviorSubject.subscribe({
    next: (val: number) => console.log('observer III: ' + val)
});
// observer III: 33 
```

请看这个例子:[https://plnkr.co/edit/HPNSAzFmLipvHQ96ZkFt?p=preview](https://plnkr.co/edit/HPNSAzFmLipvHQ96ZkFt?p=preview)

### 真实世界的例子案例

如果你总是需要一个初始值，你会想要使用一个`BehaviorSubject`。当您等待可能序列的第一个实际值时，这个初始值可以作为临时占位符。例如:

*   您正在获取一个文件的内容，并希望显示一个初始占位符，表明文件正在加载。
*   在您有效地开始接收实际值之前，您的聊天框会显示一条欢迎消息。

## 什么是`ReplaySubject`？

一个`ReplaySubject`有点类似于一个`BehaviorSubject`。它可以将以前的值发送给新加入的订户，但是它也可以记录一部分`Observable`执行。`ReplaySubject`记录来自`Observable`执行的多个值，并将它们重放给任何新连接的`Subscribers`。

创建`ReplaySubject`时，构造函数中的第一个参数定义了要重放多少个值。第二个参数指定了以毫秒为单位的窗口时间，除了缓冲区大小之外，它还决定了记录值的最大生命周期。

### 举例

在下面的例子中，我们使用了一个相当大的缓冲区大小 25，但是窗口时间参数在 1000 毫秒或 1 秒后使缓冲区中记录的项目无效。因此，如果我们使用每 800 毫秒 3 个点的特定时间线发出一些值，则只有在最后一个时间帧发出的值会被重放，因为所有其他值在 2000 毫秒后都会失效。在完整的时间范围内，第一批在 1000 毫秒后，第二批在 2000 毫秒后。

```
import { ReplaySubject } from 'rxjs/ReplaySubject';
let replaySubject = new ReplaySubject<number>(25, 1000);

setTimeout(() => {
    replaySubject.next(1);
    replaySubject.next(2);
    replaySubject.next(3);
}, 0);

setTimeout(() => {
    replaySubject.next(4);
    replaySubject.next(5);
    replaySubject.next(6);
}, 800);

setTimeout(() => {
    replaySubject.next(7);
    replaySubject.next(8);
    replaySubject.next(9);
}, 1600);

setTimeout(() => {

    replaySubject.subscribe(
      (val: number) => console.log('observer: ' + val)
  );

}, 2000);
// log: observer: 7
// log: observer: 8
// log: observer: 9 
```

请看这个例子:[https://plnkr.co/edit/dfCLm3Eh2c39JKOlGLZy?p=preview](https://plnkr.co/edit/dfCLm3Eh2c39JKOlGLZy?p=preview)

### 真实世界的例子案例

如果您需要在每次调用应用程序的特定部分时重放一组动作或值，那么您通常需要一个`ReplaySubject`。例如:

*   您可以在您的应用程序中使用一个`ReplaySubject`来显示最近的 5 个通知，每次用户回到他的仪表板。
*   你可以在你的聊天应用程序中使用一个`ReplaySubject`来显示特定用户的最后 5 条消息，每次你打开他的聊天框。

## 什么是`AsyncSubject`？

`AsyncSubject`是`Subject`的替代实现，其中只有最后一个值被发送给它的观察者，并且只有当执行完成时。这个最后的值被永久缓存，每个订阅了`AsyncSubject`的新`Observer`将立即收到缓存的值。

`AsyncSubjects`可以作为`Promises`的替代品。唯一的区别是，`Promise`处理单个值，而`AsyncSubjects`处理多个值，只缓存和发出最后一个值。记得完成`AsyncSubject`，因为这是缓存使能器！

在下面的例子中，当我们在最后一次调用 next 后完成 subject 时，observer 的下一个函数发出的唯一值是 3。

```
import { AsyncSubject } from 'rxjs/Subject';
let asyncSubject = new AsyncSubject<number>();

asyncSubject.subscribe(
  (val: number) => console.log('observer: ' + val)
);

asyncSubject.next(1);
asyncSubject.next(2);
asyncSubject.next(3);

asyncSubject.complete();
// log: observer: 3 
```

请看这个例子:[https://plnkr.co/edit/3QAbza00V36EQv7N5seO?p=preview](https://plnkr.co/edit/3QAbza00V36EQv7N5seO?p=preview)

### 真实世界的例子案例

一个`AsyncSubject`可以用在对一个资源做多个请求的情况下。如果您的远程服务/函数使用并返回了`AsyncSubject`的单例实现，那么对该资源的后续调用将只调用一次对后端的调用。所有随后的呼叫(和用户)将被通知与单例`AsyncSubject`相同的结果。

## 更多细节和例子？

如果你知道任何类型`Subject`的其他相关例子，请告诉我！我很乐意将它们添加到示例列表中。如果你想了解更多关于`Subjects`或 RxJS 的信息，你可以通过项目的 Github 库:【https://github.com/ReactiveX/rxjs/tree/5.0.0-beta.10/doc】或官方网站的文档:[【http://reactivex.io/rxjs/identifiers.html】](http://reactivex.io/rxjs/identifiers.html)上的大量自述文件来阅读。

## 进一步阅读

*   [了解 RxJS-RxJS 的清晰示例、解释和资源。](https://www.learnrxjs.io/)
*   [angular . io-RxJS 库](https://angular.io/guide/rx-library)
*   [RxJS -深度角度](https://blog.angularindepth.com/tagged/rxjs)

*最初发布于[https://samvloeberghs . be](https://samvloeberghs.be/posts/whats-with-the-subjects-in-rxjs5)2016 年 7 月 14 日*