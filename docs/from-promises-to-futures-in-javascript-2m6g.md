# Javascript 从承诺到未来

> 原文：<https://dev.to/nadeesha/from-promises-to-futures-in-javascript-2m6g>

作为一个很早就跳上 Node.js 列车并与回调地狱搏斗的人，我非常喜欢承诺。我仍然这样做，但更多的是“这是我们最好的方式”。很多次我都忘记了承诺链，它总是无声无息地失败。或者，我不得不[设法取消承诺链](https://github.com/sindresorhus/p-cancelable)。

我不会对承诺的所有问题做过多的详细说明。但是我强烈推荐[打破承诺](https://medium.com/@avaq/broken-promises-2ae92780f33)，它很好地总结了一切。

## 乱舞

作为承诺的替代品，我已经使用 fluture 有一段时间了。为此我强烈推荐 [fluture-js](https://github.com/fluture-js/Fluture) 库。它将自己介绍为“承诺的另一种选择，符合幻想世界(monadic)”。如果你不熟悉幻境规范的单子，不用担心。

道格拉斯·克洛克福特曾经说过，“一旦有人理解了单子，他们就失去了向其他人解释的能力”。尽管如此，让我用 Javascript 术语解释一下单子是什么。

在 Javascript 世界中，monad 往往是一个拥有一堆函数作为属性的对象。当您调用这些函数时，它往往会做一些事情并返回类似于原始 Javascript 对象的内容。然后，您可以使用返回的对象做进一步的计算。这听起来像是一个承诺，但是[承诺并不是单子](https://stackoverflow.com/questions/45712106/why-are-promises-monads),原因我们不在这里讨论。

## 举例

让我们考虑一个简单的期货例子(使用 fluture-js ),其中我们:

1.  读取并解析 package.json 文件以获取包的名称
2.  将数据发送到一个虚构的 API，该 API 返回一些元数据
3.  解析结果以获得下载计数
4.  将该计数发送给一个`onSuccess`函数

```
import { encaseP, node, encase } from "fluture";

const readFileF = filePath => node(done => fs.readFile(filePath, "utf8", done));

const fetchF = url => encaseP(url => fetch(url));

const jsonParseF = encase(jsonString => JSON.parse(jsonString));

const getPackageDownloads = (npmPackageName, onSuccess, onFailure) => {
  readFileF("package.json")
    .pipe(chain(jsonParseF)) // shorthand for chain(jsonString => jsonParseF(jsonString))
    .pipe(map(package => package.name))
    .pipe(
      chain(packageName => fetchF(`/api/v1/package-metadata/${packageName}`))
    )
    .pipe(map(jsonParseF))
    .pipe(map(response => response.data.metadata.downloadCount))
    .pipe(
      fork(error => onFailure(error), downloadCount => onSuccess(downloadCount))
    );
}; 
```

## 解释

我愿意相信，通过了解 fluture 提供的 20%的构造，你可以获得它提供的 80%的价值。这很可能是那 20%。让我们详细检查一下前面例子中的每个构造，看看它做了什么。

### 0。所有构造通用...

...一切都有回报的事实。因此，我们可以组合这些未来，重构它们，等等。

### 1。包装，节点，包装创造未来

由于 javascript 没有未来，我们需要一些工具来将现有的 Javascript 结构(如承诺和函数)转换成未来。这三个结构正是这样做的。

1.  包装从承诺中创造未来
2.  节点从节点风格的异步函数中创造未来
3.  encase 从一个普通的旧 javascript 函数创造了一个未来

因为所有这三个 Javascript 构造都可能因为承诺拒绝、回调(错误)或异常而失败，所以这三个实用程序也将拒绝状态映射到未来的拒绝。

### 2。pipe 让你连锁期货

把这个想象成你反转`compose()`函数时得到的`pipe()`。我们在[《编写更多函数式 JavaScript 实用指南》](https://write.as/nadeesha/practical-functional-programming-javascript)中详细讨论了这一点。它基本上让你“链”期货。

### 3。映射转换值

当你必须通过管道传输某些东西时，你总是必须通过管道传输你的结果的一个`future`版本。当你的计算没有产生未来时，像`package => package.name`，你可以在`map`内部进行转换。

当您调用带有未来的`map(fn)`时，`map`获取该未来内的值，应用`fn`转换该值，并返回包装在未来中的值。

### 4。连锁改变价值观，但期待未来的回报

`chain`做同样的事情地图没有，但是你`chain(fn)`里的`fn`必须还未来回去。

### 5。要执行的分叉

因为期货被延迟评估，所以在你告诉期货去执行事情之前，什么都没做。`fork(failureFn, successFn)`获取一个成功函数和一个失败函数，并在一个成功/失败实例上执行它们。

## 为什么用期货胜过承诺？

使用期货有很多好处。美观的 API 是其中很大的一部分。既然承诺是真正的竞争，让我试着对承诺做一些具体的区分。

1.  **懒评**有很多实用的优点。你可以保证你的计算不会在创造未来的时候执行。而`new Promise(...)`将在创建时执行。

2.  可测试性也来自懒惰评估。测试时不要嘲笑你所有的副作用，你可以断言包装副作用的期货是否是“合成的”。从而也不会产生副作用。

3.  **比承诺更好的控制流量**。您可以`race()`、`parallel()` -ize 和取消一个或多个现成的承诺。

4.  **错误处理**要优越得多。你将总是以一个错误结束。不再有被遗忘的无声压制错误。你会得到开箱即用的真正好的[调试体验](https://github.com/fluture-js/Fluture#debugging)。