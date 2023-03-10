# 2019 年要破解的 35 个最有棱角的 7 个面试问题

> 原文：<https://dev.to/fullstackcafe/35-top-angular-7-interview-questions-to-crack-in-2019-598g>

[![35 Top Angular 7 Interview Questions To Crack in 2019](img/2d7680d8f48afb4b3fd525a6f190adb2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_gXEKR6o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/2116475/pexels-photo-2116475.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26w%3D350) 
谷歌终于在 2018 年 10 月 18 日发布了 Angular 7。Angular 7 现在支持 Typescript 3.1、RxJS 6.3 和 Node 10。让我们重温一下你的角度知识，学习一些你在下一次角度面试中可能遇到的最新问题&。

> 最初发表于 [FullStack。永远不要再错过你的技术面试](https://www.fullstack.cafe)

### Q1:什么是管道？举个例子吧。

> 题目:**棱角分明**T2】难度:⭐

一个**管道**接收数据作为输入，并将其转换为所需的输出。您可以用潜在的有用组合将管道连接在一起。您可以编写自己的自定义管道。Angular 带有一系列管道，如`DatePipe`、`UpperCasePipe`、`LowerCasePipe`、`CurrencyPipe`和`PercentPipe`。

考虑:

```
<p>The hero's birthday is {{ birthday | date }}</p> 
```

在这个页面中，您将使用管道将组件的生日属性转换成一个对人友好的日期。

🔗**来源:** [angular.io](https://angular.io/guide/pipes)

### Q2:组件的最小定义是什么？

> 题目:**棱角分明**T2】难度:⭐⭐

角度中`@Component`的绝对最小配置是一个模板。两个模板属性都被设置为可选的，因为您必须定义`template`或`templateUrl`。

当你不定义它们的时候，你会得到一个这样的异常:

```
No template specified for component 'ComponentName' 
```

选择器属性不是必需的，因为您也可以在路线中使用组件。

🔗**来源:**【stackoverflow.com】T2

### Q3:角分量和模数有什么区别？

> 题目:**棱角分明**T2】难度:⭐⭐

*组件*控件视图(html)。它们还与其他组件和服务通信，为您的应用程序带来功能。

*模块*由一个或多个组件组成。他们不控制任何 html。您的模块声明哪些组件可以被属于其他模块的组件使用，哪些类将被依赖注入器注入，以及哪些组件将被引导。模块允许你管理你的组件，给你的应用带来模块化。

🔗**来源:**【stackoverflow.com】T2

### Q4:如何在组件模板中选择元素？

> 题目:**棱角分明**T2】难度:⭐⭐

您可以通过 ElementRef 将 DOM 元素的句柄注入到组件的构造函数中:

```
constructor(myElement: ElementRef) { ... } 
```

🔗**来源:**【medium.com】T2

### Q5:什么是观察者？

> 题目:**棱角分明**T2】难度:⭐⭐

Observer 是一个由可观察对象提供的基于推送的通知的消费者接口。它具有以下结构，

```
 interface Observer<T> {
      closed?: boolean;
      next: (value: T) => void;
      error: (err: any) => void;
      complete: () => void;
    } 
```

实现用于接收可观察通知的 Observer 接口的处理程序将作为 observable 的参数传递，如下所示，

```
 myObservable.subscribe(myObserver); 
```

**注意:**如果您没有为通知类型提供一个处理程序，观察者将忽略该类型的通知。

🔗**来源:**【github.com/sudheerj】T2

### Q6:什么是可观测？

> 题目:**棱角分明**T2】难度:⭐⭐

可观察对象是一个独特的对象，类似于可以帮助管理异步代码的承诺。Observable 不是 JavaScript 语言的一部分，所以我们需要依赖一个流行的名为 RxJS 的 Observable 库。使用新关键字创建观察值。让我们看看可观测的简单例子，

```
 import { Observable } from 'rxjs';

    const observable = new Observable(observer => {
      setTimeout(() => {
        observer.next('Hello from a Observable!');
      }, 2000);
    });` 
```

🔗**来源:**【github.com/sudheerj】T2

### Q7:什么是 TestBed？

> 题目:**棱角分明**T2】难度:⭐⭐⭐

**角度试验台(ATB)** 是一个更高级别的*角度专用*测试框架，允许我们轻松测试依赖于角度框架的行为。

我们仍然用 Jasmine 编写我们的测试，用 Karma 运行，但是我们现在有了一种更简单的方法来创建组件、处理注入、测试异步行为以及与我们的应用程序交互。

测试床创建一个动态构建的角度测试模块，模拟角度`@NgModule`。

🔗**来源:** [angular.io](https://angular.io/guide/testing)

### Q8:Redux 是什么，和一个有角度的 app 有什么关系？

> 题目:**棱角分明**T2】难度:⭐⭐⭐

**Redux** 是一种管理应用程序状态和提高应用程序异步可维护性的方法，它为应用程序状态提供了单一的真实来源，并在应用程序中提供了单向的数据变更流。`ngrx/store`是 Redux 原则的一个实现。

🔗**来源:**【github.com/WebPredict】T2

### Q9:Angular 7 的核心依赖有哪些？

> 题目:**棱角分明**T2】难度:⭐⭐⭐

有两种类型的核心依赖，RxJS 和 TypeScript。

*   **RxJS 6.3**-RxJS 6.3 版本被 Angular 7 使用。它与 Angular 6 相比没有任何变化

*   **TypeScript 3.1**-TypeScript 3.1 版本由 Angular 7 使用。它是 Angular 6 的 2.9 版本的升级。

🔗**来源:**[onlineinterviewquestions.com](https://www.onlineinterviewquestions.com/angular-7-interview-questions/)

### Q10:为什么增量 DOM 内存占用低？

> 题目:**棱角分明**T2】难度:⭐⭐⭐

每次重新渲染时，Virtual DOM 会从头开始创建一整棵树。

另一方面，增量 DOM 不需要任何内存来重新呈现视图，如果它不改变 DOM 的话。我们只需要在添加或删除 DOM 节点时分配内存。并且分配的大小与 DOM 变化的大小成比例。

🔗**来源:** [blog.nrwl.io](https://blog.nrwl.io/understanding-angular-ivy-incremental-dom-and-virtual-dom-243be844bf36)

### Q11:控制 AOT 编译有哪些方法？

> 题目:**棱角分明**T2】难度:⭐⭐⭐

您可以通过两种方式控制您的应用程序编译

1.  通过在`tsconfig.json`文件中提供模板编译器选项
2.  通过用装饰器配置角度元数据

🔗**来源:**【github.com/sudheerj】T2

### Q12:什么是激活路由？

> 题目:**棱角分明**T2】难度:⭐⭐⭐

**ActivatedRoute** 包含与插座中加载的组件相关联的路线的信息。它还可以用来遍历路由器状态树。ActivatedRoute 将作为路由器服务被注入以访问信息。在下面的例子中，你可以访问路线路径和参数，

```
@Component({
    ...
 })
class MyComponent {
    constructor(route: ActivatedRoute) {
        const id: Observable < string > = route.params.pipe(map(p => p.id));
        const url: Observable < string > = route.url.pipe(map(segments => segments.join('')));
        // route.data includes both `data` and `resolve`
        const user = route.data.pipe(map(d => d.user));
    }
} 
```

🔗**来源:**【github.com/sudheerj】T2

### Q13:什么是路由器出口？

> 题目:**棱角分明**T2】难度:⭐⭐⭐

**route outlet**是来自路由器库的指令，它充当占位符，标记模板中路由器应该显示该插座组件的位置。路由器插座用作组件，

```
 <router-outlet></router-outlet>
    <!-- Routed components go here --> 
```

🔗**来源:**【github.com/sudheerj】T2

### Q14:RxJS 提供的效用函数有哪些？

> 题目:**棱角分明**T2】难度:⭐⭐⭐

RxJS 库还提供了下面的实用函数，用于创建和处理观察值。

1.  将异步操作的现有代码转换为可观察代码
2.  遍历流中的值
3.  将值映射到不同的类型
4.  过滤流
5.  构成多个流

🔗**来源:**【github.com/sudheerj】T2

### Q15:什么是组播？

> 题目:**棱角分明**T2】难度:⭐⭐⭐

多点传送是在一次执行中向多个用户列表广播的实践。让我们来演示多点传送特性，

```
 var source = Rx.Observable.from([1, 2, 3]);
    var subject = new Rx.Subject();
    var multicasted = source.multicast(subject);

    // These are, under the hood, `subject.subscribe({...})`:
    multicasted.subscribe({
      next: (v) => console.log('observerA: ' + v)
    });
    multicasted.subscribe({
      next: (v) => console.log('observerB: ' + v)
    });

    // This is, under the hood, `s 
```

🔗**来源:**【github.com/sudheerj】T2

### Q16:什么是订阅？

> 题目:**棱角分明**T2】难度:⭐⭐⭐

只有当有人订阅时，一个可观察的实例才开始发布值。因此，您需要通过调用实例的 **subscribe()** 方法进行订阅，传递一个 observer 对象来接收通知。

让我们以创建和订阅一个简单的可观察对象为例，使用一个观察器将收到的消息记录到控制台。

```
 Creates an observable sequence of 5 integers, starting from 1
    const source = range(1, 5);

    // Create observer object
    const myObserver = {
      next: x => console.log('Observer got a next value: ' + x),
      error: err => console.error('Observer got an error: ' + err),
      complete: () => console.log('Observer got a complete notification'),
    };

    // Execute with the observer object and Prints out each item
    myObservable.subscribe(myObserver);
    // => Observer got a next value: 1
    // => Observer got a next value: 2
    // => Observer got a next value: 3
    // => Observer got a next value: 4
    // => Observer got a next value: 5
    // => Observer got a complete notification 
```

🔗**来源:**【github.com/sudheerj】T2

### Q17:如何在 Angular 中为每个请求设置头？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐

您可以提供一个服务，从 Angular 包装原始的`Http`对象。

```
import { Injectable } from '@angular/core';
import { Http, Headers } from '@angular/http';

@Injectable() export class HttpClient {
    constructor(private http: Http) {}

    createAuthorizationHeader(headers: Headers) {
        headers.append('Authorization', 'Basic ' + btoa('username:password'));
    }

    get(url) {
        let headers = new Headers();
        this.createAuthorizationHeader(headers);
        return this.http.get(url, {
            headers: headers
        });
    }

    post(url, data) {
        let headers = new Headers();
        this.createAuthorizationHeader(headers);
        return this.http.post(url, data, {
            headers: headers
        });
    }
} 
```

你可以注入这个对象(`HttpClient`)，而不是注入`Http`对象。

```
import { HttpClient } from './http-client'; 
```

🔗**来源:**【medium.com】T2

### Q18:为什么会使用渲染器方法而不使用原生元素方法？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐

Angular 是一个平台，浏览器只是我们可以呈现应用程序的一个选项。当我们直接访问原生元素时，我们放弃了 Angular 的 DOM 抽象，错过了在非 DOM 环境中执行的机会，例如:

*   原生手机，
*   本机桌面，
*   网络工作者
*   服务器端渲染。

`Renderer2`类是 Angular 以服务的形式提供的一个抽象，它允许在不直接接触 DOM 的情况下操作应用程序的元素。这是推荐的方法，因为这样可以更容易地开发可以在没有 DOM 访问的环境中呈现的应用程序，比如在服务器、web worker 或本地移动设备上。

🔗**来源:** [alligator.io](https://alligator.io/angular/using-renderer2/)

### Q19:什么是带角的？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐

`NgZone`是围绕`Zone.js`的包装器，它是一个围绕异步函数创建上下文的库，目的是使它们可跟踪。Angular 的变化检测严重依赖于区域。

🔗**来源:**【stackoverflow.com】T2

### Q20:实时(JIT)编译器(一般)做什么？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐

JIT 编译器在程序启动后运行**,将代码(通常是字节码或某种 VM 指令)动态地(或称之为实时地)编译成通常更快的形式，通常是主机 CPU 的本机指令集。JIT 可以访问动态运行时信息，而标准编译器不能，并且可以进行更好的优化，比如经常使用的内联函数。**

这与传统的编译器不同，传统的编译器在程序第一次运行之前将所有代码编译成机器语言。

🔗**来源:**【stackoverflow.com】T2

### Q21:ngUpgrage 是什么？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐

Angular 团队开发了一个库，我们可以在应用中使用它来混合和匹配 AngularJS 和 Angular 组件，并连接 AngularJS 和 Angular dependency 注入系统。

🔗**来源:** [blog.nrwl.io](https://blog.nrwl.io/ngupgrade-in-depth-436a52298a00)

### Q22:为什么会在 Angular app 中使用懒加载模块？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐

要延迟加载一个功能模块，我们需要使用路由配置中的`loadChildren`属性加载它，并且该功能模块不得导入应用模块。当应用程序越来越大时，延迟加载非常有用。在延迟加载中，功能模块将按需加载，因此应用程序启动会更快。

```
const routes: Routes = [
  {
    path: 'customers',
    loadChildren: 'app/customers/customers.module#CustomersModule'
  },
  {
    path: 'orders',
    loadChildren: 'app/orders/orders.module#OrdersModule'
  },
  {
    path: '',
    redirectTo: '',
    pathMatch: 'full'
  }
]; 
```

🔗**来源:**【concretepage.com】T2

### Q23:什么是常春藤渲染器？有角 7 支持吗？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐

Angular view engine 的目的是将我们编写的模板和组件转换成常规的 HTML 和 JavaScript，以便浏览器轻松阅读。Ivy 是下一代角度渲染器。它排在原始编译器(对于 Angular 2)和渲染器 2(对于 Angular 4 和更高版本)之后的第三位。

Angular Ivy 是一个新的 Angular 渲染器，它与我们在主流框架中看到的任何东西都完全不同，因为它使用了增量 DOM。不，它还没有发布。

🔗**来源:**【onlineinterviewquestions.com】T2

### Q24:什么是增量 DOM？和虚拟 DOM 有什么区别？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐

**增量 DOM** 在谷歌内部使用，它是由这个关键思想定义的:

每个组件都被编译成一系列指令。这些指令创建 DOM 树，并在数据发生变化时就地更新它们。

React 是第一个使用**虚拟 DOM**的主流框架，它是由以下关键思想定义的:

每次重新渲染时，每个组件都会创建一个新的虚拟 DOM 树。React 将新的虚拟 DOM 树与旧的进行比较，然后对浏览器 DOM 应用一系列转换以匹配新的虚拟 DOM 树。

🔗**来源:** [blog.nrwl.io](https://blog.nrwl.io/understanding-angular-ivy-incremental-dom-and-virtual-dom-243be844bf36)

### Q25:与 AOT 有什么优势？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐

以下是 AOT 福利列表，

1.  **更快的渲染:**浏览器下载应用程序的预编译版本。因此它可以立即渲染应用程序，而无需编译应用程序。
2.  **更少的异步请求:**它将外部 HTML 模板和 CSS 样式表内嵌在应用程序 javascript 中，消除了单独的 ajax 请求。
3.  **更小的 Angular 框架下载大小:**不需要下载 Angular 编译器。因此，它大大减少了应用程序的有效负载。
4.  **更早地检测模板错误:**在构建步骤中检测并报告模板绑定错误
5.  **更好的安全性:**它将 HTML 模板和组件编译成 JavaScript。所以不会有什么注射攻击。

🔗**来源:**【github.com/sudheerj】T2

### Q26:我需要引导自定义元素吗？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐

不，自定义元素在添加到 DOM 时会自动引导(或启动),在从 DOM 中移除时会自动销毁。一旦自定义元素被添加到任何页面的 DOM 中，它的外观和行为就像任何其他 HTML 元素一样，并且不需要任何 Angular 的特殊知识。

🔗**来源:**【github.com/sudheerj】T2

### Q27:纯的和不纯的管子有什么区别？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐

*   仅当 Angular 检测到传递给管道的值或参数发生变化时，才会调用**纯管道**。例如，对原始输入值(字符串、数字、布尔值、符号)或已更改的对象引用(日期、数组、函数、对象)的任何更改。
*   无论值或参数是否发生变化，每个变化检测周期都会调用一个**不纯管道**。也就是说，不纯的管道经常被调用，就像每次击键或鼠标移动一样频繁。

🔗**来源:**【github.com/sudheerj】T2

### Q28:行为主体与可观察主体的区别？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐⭐

行为主体是一种主体，主体是一种特殊类型的可观察对象，所以你可以像其他可观察对象一样订阅消息。BehaviorSubject 的独特之处在于:

*   它需要一个初始值，因为它必须总是返回一个订阅值，即使它没有收到一个`next()`
*   订阅时，它返回主题的最后一个值。常规可观察对象只有在收到`onnext`时才会触发
*   在任何时候，您都可以使用`getValue()`方法在不可观察的代码中检索主题的最后一个值。

与可观察对象相比，受试者的独特特征是:

*   除了是一个可观察对象之外，它还是一个观察者，所以除了订阅它之外，您还可以向主题发送值。

此外，您可以使用 BehaviorSubject 上的`asobservable()`方法从 behavior subject 获得一个可观察值。

在角度服务中，我会将 BehaviorSubject 用于数据服务，因为角度服务通常会在组件和行为主体初始化之前确保使用该服务的组件收到最后更新的数据，即使在组件订阅该数据后没有新的更新。

🔗**来源:**【medium.com】T2

### Q29:Angular js " $ watch "的角度当量是多少？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐⭐

解决方案是来自 ES6 的`set`语法。`set`语法将一个对象属性绑定到一个函数，当试图设置该属性时，该函数将被调用。

```
import { Component, Input } from '@angular/core';
@Component({
  selector: 'example-component',
})
export class ExampleComponent {
  public internalVal = null;
  constructor() {}

  @Input('externalVal')
  set updateInternalVal(externalVal) {
    this.internalVal = externalVal;
  }
} 
```

🔗**来源:**【medium.com】T2

### Q30:说出 SystemJS 和 WebPack 的一些区别？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐⭐

**系统 JS** :

*   众所周知，SystemJS 对自己的 polyfill 使用的依赖关系很奇怪
*   从 npm 导入库/模块不像使用 WebPack 那么简单
*   随着您添加依赖项和代码开始增长，JSPM( systemjs)的一个致命缺陷就暴露出来了。它变得非常迟钝。一次刷新大约需要 15-20 秒，因为浏览器将加载数百个不同的 JavaScript 文件
*   仍然需要吞咽:
    *   缩小
    *   羡慕(羡慕)
    *   为包文件生成唯一的哈希名称

**网络包**:

*   变化现在以毫秒显示；Webpack 的开发服务器是为速度而设计的。它不仅支持增量构建，而且直接从内存中提供服务
*   您可以轻松地从 npm(例如 Bootstrap 或 Foundation)导入库，而不必担心它们在 node_modules 中的确切路径
*   不需要吞咽。Webpack 本身负责完成 SystemJS 中所有需要 Gulp 的任务
*   看起来 Webpack 是为大型应用程序从头开始设计的，这在开发过程中有所体现

🔗**来源:**【vteams.com】T2

### Q31:准时制(JiT) vs 提前制(AoT)编译。解释区别。

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐⭐

JIT -及时编译 TypeScript 以执行它:

*   在浏览器中编译。
*   每个文件单独编译。
*   无需在更改代码后和重新加载浏览器页面前进行构建。
*   适合本地发展。

**AOT**——在构建阶段编译类型脚本:

*   由机器自己编译，通过命令行(更快)。
*   所有代码一起编译，在脚本中内嵌 HTML/CSS。
*   不需要部署编译器(角度大小的一半)。
*   更安全，原始来源没有透露。
*   适用于生产版本。

🔗**来源:**【stackoverflow.com】T2

### Q32:angular 为什么用 url 段？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐⭐

一个 **UrlSegment** 是两个斜杠之间的 URL 的一部分。它包含一条路径和与该段相关的*矩阵参数*。

矩阵参数依赖于路径段，而查询参数依赖于 URL。它们有不同的语义。

考虑:

```
localhost:3000/heroes;id=15;foo=foo/bar/baz
// instead of localhost:3000/heroes/bar/baz?id=15&foo=foo 
```

参数*绑定到英雄*不绑定到 URL。当你访问 route.url 时，你会看到这个

```
this.route.url.subscribe((url: UrlSegment[]) => {
  let heroes = url[0];
  let heroesMatrix = heroes.parameters();
  // heroes should contain id=15, foo=foo
  let bar = url[1].path; // 15
  let baz = url[2].path; //foo
}) 
```

对于矩阵参数，您也可以订阅参数，而不是将它们从 url 中剥离出来。

```
this.paramSubscription = this.activeRoute.params.subscribe(params => {
  const bar = params['bar'];
  const baz = params['baz'];
}); 
```

有了 Angular app，真正关心这些参数的只有我们这些开发者。用户不在乎。它不是一个我们应该坚持众所周知的语义的 REST API。对于我们的 Angular 应用程序，只要我们开发人员知道如何使用参数(无论是矩阵还是查询)，我们使用哪一个都不重要。

🔗**来源:**【https://stackoverflow.com】T2

### Q33:Google 团队为什么用增量 DOM 而不是虚拟 DOM？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐⭐

他们心中只有一个目标:**应用程序必须在移动设备上运行良好**。这主要意味着优化两件事:包的大小和内存占用。

为了实现这两个目标:

*   渲染引擎本身必须是树可摇动的
*   渲染引擎必须具有低内存占用

🔗**来源:** [blog.nrwl.io](https://blog.nrwl.io/understanding-angular-ivy-incremental-dom-and-virtual-dom-243be844bf36)

### Q34:为什么增量 DOM 是树摇的？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐⭐

当使用增量 DOM 时，框架不解释组件。相反，组件引用指令。如果它不引用特定的指令，它将永远不会被使用。因为我们在编译时知道这一点，所以我们可以从包中省略未使用的指令。

虚拟 DOM 需要一个解释器。在编译时不知道解释器的哪一部分是需要的，哪一部分是不需要的，所以我们必须把全部内容发送给浏览器。

🔗**来源:** [blog.nrwl.io](https://blog.nrwl.io/understanding-angular-ivy-incremental-dom-and-virtual-dom-243be844bf36)

### Q35:Angular 7 有什么新功能？

> 题目:**棱角分明**T2】难度:⭐⭐⭐⭐⭐

*   angular Elements——现在支持使用定制元素的 **web 标准进行内容投影。**

*   Angular 7.0 应用程序将使用 Angular CLI 的捆绑包预算功能。如果应用程序包的大小超过了预定义的限制，这将会警告开发人员。

*   角形材料的组件开发套件(CDK)也获得了一些新功能

    *   虚拟滚动
    *   拖放
*   mat-form-field 现在将支持使用本地 select 元素。这将为应用程序提供增强的性能和可用性。

*   Angular 7.0 更新了依赖关系，支持 Typescript 3.1、RxJS 6.3 和 Node 10。

*   为新应用设定预算

*   为了澄清一些问题，棱角分明的**常春藤渲染器**尚未发布。

🔗**来源:**【medium.freecodecamp.org】T2

> 谢谢🙌阅读，祝你面试好运！
> *如果你喜欢这篇文章，请分享给你的开发者伙伴！*
> *查看更多全栈面试问题&答案上👉[www . full stack . cafe](https://www.fullstack.cafe)T9】*