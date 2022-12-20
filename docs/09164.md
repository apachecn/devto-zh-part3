# 不要让这个异步/等待 Oopsie！

> 原文：<https://dev.to/mebble/dont-make-this-asyncawait-mistake-1eao>

假设我们需要对数组的项目执行一些 I/O 操作，比如使用一些 API 从猫的 id 中获取猫的主人。

```
const catIDs = [132, 345, 243, 121, 423]; 
```

Enter fullscreen mode Exit fullscreen mode

假设我们决定使用新获得的 async/await 技能来完成这项工作。Async/await 消除了对回调的需要(在大多数情况下)，使异步代码看起来类似于同步代码。但是如果我们忘记了我们仍然只是在处理异步代码，我们可能会犯一个错误，使拥有并发性的整个目的落空。

我们可能会想做这样的事情:

```
async function fetchOwners(catIDs) {
    const owners = [];
    for (const id of catIDs) {
        const cat = await fetchCat(id);
        const owner = await fetchOwner(cat.ownerID);
        owners.push(owner);
    }
    return owners;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 我们的 Oopsie 是什么？🤷‍♂️

免责声明:如果你知道 oopsie 是什么，那么你可能已经知道你在做什么。你可能知道这种行为的一个用例，所以我想称它为“oopsie”有点不公平。这篇文章只是让人们熟悉这种异步/等待行为。

我们运行代码，看起来一切正常。但是在我们如何使用 async/await 方面有一个明显的问题。问题是我们在 for 循环中使用了`await`。这个问题实际上表明了一种常见的[代码味道](https://en.m.wikipedia.org/wiki/Code_smell)，即在执行数组转换的 for-loop" 方法中 *" `.push`"到输出数组，而不是使用 [`.map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) (我们将在后面讨论)。*

由于 for 循环中的`await`，看起来同步的`fetchOwners`函数正在顺序地(某种程度上)而不是并行地执行对 cat 的提取。在进入下一个 for 循环迭代以获取下一只猫的主人之前，代码`await`代表一只猫的主人。抓住一只猫的主人并不依赖于其他任何一只猫，但我们表现得好像是这样。所以我们完全错过了并行获取所有者的能力(oopsie！🤷‍♂️).

***注:**我提到了“种”顺序，因为那些顺序的 for-loop 迭代与其他过程交错(通过[事件循环](https://youtu.be/8aGhZQkoFbQ))，因为 for-loop 迭代`await`在一个`async`函数内。*

## 我们应该做什么😎

我们不应该在 for 循环中。事实上，即使代码是同步的，如果没有 for 循环，这个问题会解决得更好。一个`.map`是合适的解决方案，因为我们正在处理的问题是一个数组转换，从一个猫 id 数组到一个所有者数组。

如果代码是同步的，这就是我们使用`.map`的方法。

```
// catIDs -> owners
const owners = catIDs.map(id => {
    const cat = fetchCatSync(id);
    const owner = fetchOwnerSync(cat.ownerID);
    return owner;
}); 
```

Enter fullscreen mode Exit fullscreen mode

由于代码实际上是异步的，我们首先需要将一个猫 id 数组转换为一个承诺数组(对猫的主人的承诺)，然后使用`await`将承诺数组解包以获得主人。为了简单起见，这段代码不处理被拒绝的承诺。

```
// catIDs -> ownerPromises -> owners
async function fetchOwners(catIDs) {
    const ownerPromises = catIDs.map(id => {
        return fetchCat(id)
            .then(cat => fetchOwner(cat.ownerID));
    });
    const owners = await Promise.all(ownerPromises);
    return owners;
} 
```

Enter fullscreen mode Exit fullscreen mode

为了进一步灵活我们的 async/await 技能，我们可以将一个`async`回调传递给 map 方法，并将该回调中的所有中间响应(这里，获取一只猫以获得它的主人的 ID)传递给`await`。记住，`async`函数返回一个承诺，所以我们仍然有一个承诺数组作为`.map`的输出。这段代码相当于前一段，但是没有丑陋的`.then`。

```
async function fetchOwners(catIDs) {
    const ownerPromises = catIDs.map(async id => {
        const cat = await fetchCat(id);
        const owner = await fetchOwner(cat.ownerID);
        return owner;
    });
    const owners = await Promise.all(ownerPromises);
    return owners;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `.map`到底在做什么？

`.map`在每个 cat ID 上依次调用回调(在回调中我们发出一个 I/O 请求)。但是由于回调返回一个承诺(或者是一个异步函数)，所以`.map`不会等待一只猫的响应到达，就发出下一只猫的请求。

> 请求是按顺序发出的，但是以平行的方式行进，它们的响应是无序的(想象一下用一只手在背后扔一堆飞去来器)。

因此，我们现在像预期的那样并行获取猫主人🙌！噢，松开了！

* * *

这是我的第一篇文章。不仅仅是我的第一篇关于 DEV 的文章，也是我的第一篇博文。希望你喜欢。