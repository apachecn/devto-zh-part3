# 空闲地图

> 原文：<https://dev.to/alexsasharegan/idle-map-cl>

当您有异步运行的代码时，您有几个选择。你可以用`Promise`工作，用`setTimeout`安排稍后运行，或者通过`requestAnimationFrame`安排与浏览器渲染周期的协调。它们各有所长，但是现在我们的异步工具包中有了一个新工具:`requestIdleCallback`。我想展示一个技巧，将基于承诺的任务与新的`requestIdleCallback` API 混合起来(我们姑且称之为`rIC`)。

如果你想要一本关于`rIC`的入门书，可以看看来自[保罗·路易斯](https://developers.google.com/web/resources/contributors/paullewis)的[谷歌文章](https://developers.google.com/web/updates/2015/08/using-requestidlecallback)。您还可以从 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestIdleCallback) 上获得完整的 API 概要，以及来自[can use](https://caniuse.com/#feat=requestidlecallback)的浏览器支持信息。

这个想法是通过一个处理函数运行一个条目列表——本质上类似于`Array#map`——除了我们希望确保我们间歇地将控制权交还给主线程，以保持对用户事件的响应。我们可以使用`rIC`来安排每个项目的处理，并检查`IdleDeadline`来查看是否有更多的时间来处理另一个项目。如果没有，我们可以安排另一次空闲回拨。我们将继续这个过程，直到列表中的每一项都被处理完。

```
function idleMap(iterable, processCallback) {
  return new Promise(resolve => {
    let results = [];
    let iterator = iterable[Symbol.iterator]();

    async function processList(idleDeadline) {
      do {
        let iterResult = iterator.next();
        if (iterResult.done) {
          return resolve(results);
        }

        results.push(await processCallback(iterResult.value));
      } while (!idleDeadline.didTimeout);

      requestIdleCallback(processList);
    }

    requestIdleCallback(processList);
  });
} 
```

这个函数，`idleMap`接受你的列表(`iterable`)和一个回调(`processCallback`)，它将回调应用到列表中的每一项，就像`Array#map`一样。在内部，它通过定义一个闭包(`processList`)来使用递归，首先用一个空闲回调来调度这个闭包。一旦浏览器调用了这个函数，它就使用迭代器从列表中取出条目，并对它们应用处理回调。在每一项之后，`do..while`控件将评估空闲截止时间是否已过。如果没有，该函数可以自由处理另一项。如果截止日期已经到期，`do..while`控件中断并安排另一个空闲回调来继续处理列表。一旦消耗完列表迭代器，从`idleMap`返回的承诺将解析每个条目的处理结果。

我发现使用[迭代器接口](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)可以很好地处理`do..while`控制流，不需要管理数组索引。最大的好处是，这也意味着我们可以映射任何满足 iterable 接口的东西。这可能是双重有用的，因为它允许使用生成器函数、自定义对象和各种其他非数组类型来提供要处理的项目。