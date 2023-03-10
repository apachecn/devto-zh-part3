# 实现承诺. all

> 原文：<https://dev.to/copperwall/-implementing-promiseall-2f2b>

[![promise-all](img/8f664c9a1137a3806a65ec813876d5dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xTOHm_8U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-2.amazonaws.com/copperwall-blog-images/copperwall-dev/promise-all.png)

promise.all 是 Promise 对象上的一个静态方法，它接受一个项目列表并返回一个 Promise，该 Promise 通过一个包含输入列表中所有已解析值的列表进行解析。如果有任何值是拒绝的承诺，则返回的承诺也将被拒绝，并带有首先被拒绝的承诺的拒绝消息。当您想同时运行多个承诺，但要等到所有承诺都实现后再继续时，这尤其有用。

如果您在代码中直接使用承诺，那么您可以编写类似这样的代码，向不同的 API 端点发出多个并发请求，然后等待所有请求都处理完响应。

```
Promise.all([
    fetch('/api/a'),
    fetch('/api/b'),
    fetch('/api/c')
]).then([responseA, responseB, responseC] => {
    // Use the responses from all three async requests.
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果希望多个异步函数调用并发运行，而不是顺序运行，也可以在异步函数中使用 Promise.all。在下面的代码片段中，发出了两个网络请求，但是第二个请求直到第一个请求完成后才开始。这意味着，如果第一个请求需要五秒钟，第二个请求需要五秒钟，那么请求完成后的部分将必须等待请求时间的总和才能运行。

```
// This waits for the result of the first fetch
// before starting the second fetch.
async function doSomeThings() {
    const result1 = await fetch('/api/a');
    const result2 = await fetch('/api/b');

    return {
        ...result1,
        ...result2
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，两个网络请求同时发起。如果两个请求都需要五秒钟，则请求完成后的部分将必须等待最大请求时间才能运行。

```
// Both fetches are initiated concurrently, but the function waits for
// both to complete before continuing.
async function doSomeThings() {
    // Because we don't await the fetch,
    // we get the promise, not the resolved value.
    const resultPromise1 = fetch('/api/a');
    const resultPromise2 = fetch('/api/b');

    // We can await the resulting promise from the
    // two fetches before continuing.
    try {
        const [ result1, result2 ] = await Promise.all([resultPromise1, resultPromise2]);    
    } catch (e) {
        // If either request fails, we can catch the error.
        debug('There was an error', e.message);
    }

    return {
        ...result1,
        ...result2
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

重要的是要记住，如果列表中的任何值都传递给 Promise.all，那么整个结果将是一个被拒绝的承诺。

我认为回顾一下 Promise.all 的一些可能的实现会很有趣。

## 递归求解

Promise.all 可以使用递归解决方案来实现。基本情况是用空数组调用 Promise.all，在这种情况下，它返回一个解析为空数组的 Promise。否则，它获取列表中第一项的解析值，并对列表中的其余元素调用 Promise.all。

```
Promise.all = function promiseAllRecursive(values) {

    // Base case.

    if (values.length === 0) {

        return Promise.resolve([]);

    }

```
<span class="kd">const</span> <span class="p">[</span><span class="nx">first</span><span class="p">,</span> <span class="p">...</span><span class="nx">rest</span><span class="p">]</span> <span class="o">=</span> <span class="nx">values</span><span class="p">;</span>

<span class="c1">// Calling Promise.resolve on the first value because it could</span>
<span class="c1">// be either a Promise or an actual value.</span>
<span class="k">return</span> <span class="nb">Promise</span><span class="p">.</span><span class="nx">resolve</span><span class="p">(</span><span class="nx">first</span><span class="p">).</span><span class="nx">then</span><span class="p">(</span><span class="nx">firstResult</span> <span class="o">=&gt;</span> <span class="p">{</span>
    <span class="k">return</span> <span class="nx">promiseAllRecursive</span><span class="p">(</span><span class="nx">rest</span><span class="p">).</span><span class="nx">then</span><span class="p">(</span><span class="nx">restResults</span> <span class="o">=&gt;</span> <span class="p">{</span>
        <span class="k">return</span> <span class="p">[</span><span class="nx">firstResult</span><span class="p">,</span> <span class="p">...</span><span class="nx">restResults</span><span class="p">];</span>
    <span class="p">});</span>
<span class="p">});</span> 
```

    Enter fullscreen mode 

    Exit fullscreen mode 

 } 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
迭代求解

对于迭代解决方案，您将希望返回一个新的承诺，该承诺仅在所提供的数组的每个值都被解析后才被解析，如果任何承诺被拒绝，则该承诺被拒绝。

提供给函数的 executor 函数可以跟踪每个承诺解析的结果，并跟踪已经解析的承诺的数量。您可以使用 for 循环或 forEach 来遍历值列表，并对每个值调用*然后*方法，在解析时将结果添加到结果列表中。重要的是要记住 Promise.all 维护作为输入提供的 Promises 的结果顺序，所以不能在 promise 解析时就添加到结果列表中。您需要知道正在解析的承诺的索引，以便知道它在结果列表中的位置。在这个例子中，我通过将*索引*参数传递给 *forEach* 回调函数来实现这一点。

```
Promise.all = function promiseAllIterative(values) {

    return new Promise((resolve, reject) => {

       let results = [];

       let completed = 0;

```
 <span class="nx">values</span><span class="p">.</span><span class="nx">forEach</span><span class="p">((</span><span class="nx">value</span><span class="p">,</span> <span class="nx">index</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
        <span class="nb">Promise</span><span class="p">.</span><span class="nx">resolve</span><span class="p">(</span><span class="nx">value</span><span class="p">).</span><span class="nx">then</span><span class="p">(</span><span class="nx">result</span> <span class="o">=&gt;</span> <span class="p">{</span>
            <span class="nx">results</span><span class="p">[</span><span class="nx">index</span><span class="p">]</span> <span class="o">=</span> <span class="nx">result</span><span class="p">;</span>
            <span class="nx">completed</span> <span class="o">+=</span> <span class="mi">1</span><span class="p">;</span>

            <span class="k">if</span> <span class="p">(</span><span class="nx">completed</span> <span class="o">==</span> <span class="nx">values</span><span class="p">.</span><span class="nx">length</span><span class="p">)</span> <span class="p">{</span>
                <span class="nx">resolve</span><span class="p">(</span><span class="nx">results</span><span class="p">);</span>
            <span class="p">}</span>
        <span class="p">}).</span><span class="k">catch</span><span class="p">(</span><span class="nx">err</span> <span class="o">=&gt;</span> <span class="nx">reject</span><span class="p">(</span><span class="nx">err</span><span class="p">));</span>
   <span class="p">});</span>
<span class="p">});</span> 
```

    Enter fullscreen mode 

    Exit fullscreen mode 

 } 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
减速器方案

实现 Promise.all 的另一种方法是使用 reduce 函数。reduce 函数的初始值将是一个解析为空列表的承诺，与递归解决方案的基本情况类似。我们的 reducer 函数将接受一个累加器和一个值参数，累加器将解析到目前为止已解析值的所有结果，值参数是在对 Promise.all 的值列表(承诺与否)的迭代中的当前值。reducer 函数应该返回一个新的承诺，它将解析到累加器将解析到的结果列表，以及当前值将解析到的结果。当 reducer 遍历值列表时，每个返回值将是一个 promise，它解析为传递给 Promise.all 的值的结果的一个更大的子集。

我们不需要显式处理捕捉承诺拒绝，因为我们返回的承诺将被隐式拒绝。

```
Promise.all = function promiseAllReduce(values) {

    return values.reduce((accumulator, value) => {

        return accumulator.then(results => {

            return Promise.resolve(value).then(result => {

                return [...results, result];

            });

        });

    }, Promise.resolve([]));

} 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
实现自无极库

### 青鸟实现

除了大多数 JS 环境提供的 Promise 实现之外，Bluebird 是一个非常常见的 Promise 库。他们的[设计原则](http://bluebirdjs.com/docs/why-bluebird.html)包括采用务实或面向性能的方法，而不是优雅的解决方案，所以看看他们如何实现 Promise.all 应该很有趣

Bluebird 的 Promise.all 实现的工作方式是创建一个 [*PromiseArray*](https://github.com/petkaantonov/bluebird/blob/master/src/promise_array.js) 实例并从中返回一个 Promise，因此看起来大多数实现细节都将涉及从一个值列表中初始化 PromiseArray。

#### [许诺阵列](https://github.com/petkaantonov/bluebird/blob/master/src/promise_array.js#L19-L30)

PromiseArray 构造函数接受一个*值*参数，该参数可以是任何值的迭代器(包括承诺和具体值)，也可以是解析为迭代器的承诺。构造函数设置以下实例字段

*   _promise(将用于从 PromiseArray.prototype.promise()返回的新承诺)
*   _values(传递给 Promise.all 的值)
*   _length(初始化为 0，稍后设置为值的长度)
*   _totalResolved(初始化为 0，在承诺履行时递增)

初始化这些字段后，构造函数调用 _ *init* 方法。

#### [_init](https://github.com/petkaantonov/bluebird/blob/master/src/promise_array.js#L41-L86)

_ *init* 方法对输入的*值*进行一些设置和错误检查。它检查*值*是否是一个承诺，并设置承诺在承诺完成时调用 _ *init* 。它还将检查承诺是已被拒绝还是已被履行，以便它可以立即拒绝返回承诺，或者立即将 _values 字段设置为输入承诺的已履行值。

如果*值*参数不是一个承诺，Bluebird 试图将其转换为一个数组。如果转换失败，退货承诺立即被拒绝。如果列表为空，退货承诺会立即用空列表解决。

如果*值*参数是一个多于零个元素的列表，该列表被传递给 _ *迭代*方法。

#### [_ 迭代](https://github.com/petkaantonov/bluebird/blob/master/src/promise_array.js#L88-L127)

_ *iterate* 方法为 PromiseArray 做了大量繁重的工作。*值*参数中的每一项都被传递给 *tryConvertToPromise* 。如果结果是承诺，则将局部位字段变量设置为承诺的位字段，否则将位字段设置为空。该位字段随后用于确定承诺的解决状态(即，它是未决、已履行还是被拒绝)。

按照这个逻辑，有些情况下要根据承诺的已解决状态来处理该做什么。如果承诺已经履行，Bluebird 调用 _ *promiseFulfilled* 以实现的值。类似地，如果承诺已经被拒绝，蓝鸟呼叫 _ *promiseRejected* 告知拒绝原因。_ *promiseFulfilled* 和 _ *promiseRejected* 的相似之处在于，它们都增加了前面的 _ *totalResolved* 字段，但它们的不同之处在于 _ *promiseRejected* 立即拒绝结果承诺，而 _ *promiseFulfilled* 仅在已解决承诺的数量大于或等于给予 *PromiseArray* 的值的长度时才解析承诺。

有一点让我感到困惑的是，我没有看到尚未解决的承诺是如何处理的。对于*来说，有一个小例子是 _ PENDING _ AND _ WAITING*promises，它只是对该承诺调用 _ *代理*，并将该承诺作为临时占位符放在值列表中。_ *代理*以*承诺数组*和*索引*作为参数，将*承诺数组*设置为未决承诺的接收者。当待定承诺完成时，Bluebird 检查它是否有可代理的接收者，并在接收者对象上调用 _ *promiseFulfilled* 或 _ *promiseRejected* 。假设所有未决承诺最终都实现了，那么从 PromiseArray 返回的承诺将在最后一个未决承诺解决时解决。当任何待定承诺被拒绝时，从 PromiseArray 返回的承诺也将被拒绝。

希望这有点意思。Bluebird 实现的解决方案显然不像我们之前介绍的一些递归或 reducer 解决方案那样“优雅”或“简单”,但我认为看到他们选择的一些优化是很有趣的。将每个单独承诺的承诺解决方案代理回返回的承诺，这看起来是处理承诺解决方案列表的一种非常简单的方法。我想你必须在你的 promise 实现中加入接收方 promise 的概念，所以我很想知道除了 Promise.all 之外，Bluebird 是否还有其他的功能。