# 角到角偏移的前景与可观测性

> 原文：<https://dev.to/angular/promises-vs-observables-for-angularjs-to-angular-migration-4gh6>

AngularJS(Angular 1)大量使用 Promises 进行 HTTP 调用，而 Angular 将网络调用包装在 Observables 中。这导致一些开发人员在将项目从 AngularJS 迁移到 Angular 时遇到了特定的问题。我想在这里解决这些问题，并通过回顾可观察到的和承诺之间的典型差异来描述为什么它们会出现。

[![](img/572dbf4b2d959d4de9de78fb472a5ed9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1T7bmONh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Au1_Ed0HBa5iO7TFe_tkM8w.jpeg) 
*可观测量—网络开发人员的更多功能。(图片由 [mediamodifier](https://pixabay.com/ru/users/mediamodifier-1567646/) )*

> 先决条件:你应该知道 JS 的承诺

**观察和承诺—简短介绍**

乍一看——可观察值只是高级承诺:承诺发出一个值并完成(resolve)，可观察值发出 0、一个或多个值并完成(发出和完成是不同的动作)。AngularJS 和 Angular 中的 HTTP 服务只提供一个值——所以在这种情况下，两个框架的工作方式非常相似。

```
// Observables in Angular 2+
const sourse$ = this.httpServie.get('https://some_url.com')
source$.subscribe(
    (data) => handelData(data), // success handler
    (err) => handleError(err),  // error handler
    () => completeHandler() // onComplete handler
)

// Promises in AngularJS
const soursePromise = http$.get('https://some_url.com')
soursePromise.then(
    (data) => handelResolve(data), // resolve handler
    (err) => handleReject(err) // reject handler
) 
```

而且有人可能认为只要把 ***$http*** 改名为 ***this.httpService*** ， ***然后*** 改名为 ***subscribe*** 大家就都开心了。在非常简单的应用程序中，它甚至可以工作——但是如果你的应用程序做的不仅仅是“Hello world”——请注意这些差异。

#### #1 渴望 vs 懒惰

看看下面的例子:

```
//Promise-wrapped http request
saveChanges(data) {
  return $http.post('https://some_url.com', data)
}

//Observable-wrapped http request
saveChanges(data) {
  return this.httpService.post('https://some_url.com', data) // doesn't do request!
} 
```

当我调用 **saveChanges** 方法时——第一个带有承诺包装请求的例子将按预期工作。但是在秒可观察包装的例子中，什么都不会发生，因为可观察物被**懒惰评估**，而承诺被**急切评估。**

这意味着 Promises 不关心他们是否有一些订阅者来获得他们的结果。但是，可观察的事物(准确地说，是冷可观察的事物)只有在我们认同它们的情况下才会变冷。在上述情况下，您应该订阅由 **saveChanges** 函数返回的 Observable。

```
saveChanges(data).subscribe() 
```

为了保持警惕，使用来自[的 rxjs-tslint-rules](https://github.com/cartant/rxjs-tslint-rules/blob/master/source/rules/rxjsNoIgnoredObservableRule.ts) 和[的 Nicholas Jamieson](https://medium.com/@cartant) 的 rxjs-no-ignored-observable 规则。

#### **#2 承诺不可取消，可观测量可退订**

同样，从输入文本改变时我们在后端搜索的例子开始:

```
// html
<input ngKeyup="onKeyUp($event)">

//Promise-wrapped HTTP request
saveChanges(event) {
  const text = event.target.value;
  $http.get('https://some_url.com?search=' + text)
    .then((searchResult) => showSearchResult(searchResult))
} 
```

这里的缺点是什么——如果用户继续输入，您不能拒绝前一个请求的结果(去抖动会使这个问题稍微减轻，但不会消除它)。还有一个问题——竞争条件是可能的(当后面的请求结果比前面的更快返回时——所以我们得到不正确的响应显示)。

用 [**switchMap**](https://rxjs-dev.firebaseapp.com/api/operators/switchMap) 操作符
可以相当优雅地避免这种顾虑

```
// html template
<input id="search">

//Observable-wrapped HTTP request
inputElem = document.querySelector('#search');
search$ = fromEvent(inputElem, 'keyup');

ngOnInit() {

  search$.pipe( // each time new text value is emitted
    switchMap((event) => { // switchMap cancel previous request and send a new one
      const text = event.target.value;
      return this.httpService.get('https://some_url.com?search=' + text);
    })
  )
    .subscribe((newData) => this.applyNewData(newData))  // use new data
} 
```

在这里，我们将输入文本转换成可观察到的排放值。每次发送新的文本值时，switchMap operator 将取消之前的网络请求(如果尚未完成)并发送新的请求。

* * *

*Packtpub.com 和我准备了一整套* [***RxJS 课程***](https://www.udemy.com/hands-on-rxjs-for-web-development/) *还有许多其他细节，告诉你如何用这个神奇的库解决你的日常开发任务。它对初学者来说可能很有趣，但也包含高级主题。* [***看一看！***](https://www.udemy.com/hands-on-rxjs-for-web-development/)

* * *

#### #3 没有内置的承诺重试或重复逻辑。 [**重复**](https://rxjs-dev.firebaseapp.com/api/operators/repeat) **'** 和' [**重试**](https://rxjs-dev.firebaseapp.com/api/operators/retry) **'** 操作符为可观测量。

你可以用承诺实现重试逻辑，但是看起来有点麻烦:

```
var request = function() {
  $http({method: 'GET', url: path})
    .success(function(response) {
      results.resolve(response)
    })
    .error(function() {
      if (counter < MAX_REQUESTS) {
        request();
        counter++;
      } else {
        results.reject("Could not load after multiple tries");
      }
    });
};

request(); 
```

而相同代码的可观测量会短得多:

```
this.httpService.get('https://some_url.com/data').pipe(
    retry(MAX_REQUESTS)
) 
```

在我的文章中阅读更多关于重复和重试操作符的用例。

#### #4 少量承诺组合工具。Observables 为此提供了各种各样的操作符。

对于承诺，您可以组合结果的所有可能性有:

**Promise.all** —等待所有承诺被解析，然后提供结果数组。

**Promise.race** —等待其中一个承诺得到解决，并返回结果。

* * *

可观测量为组合提供了非常丰富的弹药:

*   **combineLatest(observable1，observable2，…)** —等待任何一个可观察对象发出并提供所有可观察对象最后发出的值的数组(结果:[value_obs1，value_obs2，..]).非常好，如果你应该从几个不同的来源更新页面上的新数据。

*   **observable1 . pipe(*with latest from*(observable2)**—对来自 observable 1 的每个值也提供 observable 2 的最后发出的值(结果:[value_obs1，value_obs2])。

*   **forkJoin(observable1，observable2，…)** — analog for Promise.all —等待所有可观察值完成，然后发出所有自变量可观察值的最后一个值的数组。

*   **zip** **(observable1，observable2，…)** —等待所有自变量观察值发出具有相同索引的值，并提供具有相同索引的发出值的数组(结果:[value_obs1，value_obs2，..]).

*   **race(observable1，observable2，...)—** 返回一个可观测值，该可观测值反映了第一个可观测到的发射物品的源。

*   **merge(observable1，observable2，…)——**订阅每个可观察的参数，并从所有这些参数中重新发出值。

*   **switchAll** —如果之前的观察没有完成，取消它并订阅新的。

*   **concat(** observable1，observable2，… **)** —仅在前一个可观察序列完成后开始下一个可观察序列(在每个特定的可观察序列完成后逐一发出值)

还有更多( [switchMap](https://rxjs-dev.firebaseapp.com/api/operators/switchMap) ，mergeMap， [partition](https://rxjs-dev.firebaseapp.com/api/index/function/partition) ， [iif](https://rxjs-dev.firebaseapp.com/api/index/function/iif) ， [groupBy](https://rxjs-dev.firebaseapp.com/api/operators/groupBy) ， [window](https://rxjs-dev.firebaseapp.com/api/operators/window) 等)

您可以在此了解有关这些运营商的更多信息:

1.  [学习将 RxJs 序列与超级直观的交互图相结合](https://blog.angularindepth.com/learn-to-combine-rxjs-sequences-with-super-intuitive-interactive-diagrams-20fce8e6511)
2.  [带有示例的官方文件](https://rxjs-dev.firebaseapp.com/api)
3.  “网络开发实践 RxJS”[视频课程](https://www.packtpub.com/web-development/hands-rxjs-web-development-video)。

#### #5 容易防止的竞态条件有可观察性，难以承诺。

假设我们定期向网络请求更新数据。但是在某些情况下，后面的请求结果会比前面的结果返回得更快，所以我们会将错误的(前面的)响应显示为最后一个。

```
getData() {
  $http.get('https://some_url.com/data')
    .then((searchResult) => {
        doSomething(searchResult)
    }
  })
}

setTimeout(getData, 5000); 
```

此代码可能会受到竞争条件问题的影响。

为了防止可观察包装的请求出现这种情况，我们可以使用 [concatMap](https://rxjs-dev.firebaseapp.com/api/operators/concatMap) 操作符。

```
interval(5000).pipe(
    concatMap(() => this.httpService.get('https://some_url.com/data'))
)
.subscribe(doSomethingWithData) 
```

**concatMap** 只有在前一个完成并处理后，才会进行下一个网络调用。当然，如果您不需要以前的结果，那么可以使用 switchMap(如本文的第一个示例)。

#### 结论

在从 AngularJS(使用网络调用的承诺)迁移到 Angular(使用可观察)的过程中，您应该意识到承诺和可观察之间可能存在的差异。希望我的文章能帮助你澄清这个话题。现在是时候迁徙了！

喜欢这篇文章？让我们在推特上保持联系。

本帖最初发表于 [ITNEXT](https://itnext.io/promises-vs-observables-for-angularjs-to-angular-migration-1161afacef7e) 。

* * *

*从我的* [***RxJS 视频课程的第 4 节开始，回顾 staff advances——因此，如果您已经熟悉 RxJS——您也可以找到一些对您有用的东西:高阶观察器、反模式、调度器、单元测试等！***](https://www.udemy.com/hands-on-rxjs-for-web-development/) **[***试一试***](https://www.udemy.com/hands-on-rxjs-for-web-development/) ***！*T19】****