# 野外的链表:React 钩子

> 原文：<https://dev.to/wuz/linked-lists-in-the-wild-react-hooks-3ep8>

前几天，在读了 Ali 关于链表的一篇很棒的文章之后，我正在钻研 React hooks 的一些代码:

[![aspittel](img/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel) [## 谢谢你，下一篇:链表介绍

### 阿里·斯皮特尔 12 月 5 日 185 分钟阅读

#datastructures #beginners #programming #javascript](/aspittel/thank-u-next-an-introduction-to-linked-lists-4pph)

当我深入其中时，我注意到钩子实际上是在使用链表。浏览一个数据结构的实现或用例总是很酷，所以我想就我所知，分享一下它们是如何被使用的以及为什么被使用。

## 实现

hooks 实现中有一个注释，内容是:

```
// Hooks are stored as a linked list on the fiber's memoizedState field. The
// current hook list is the list that belongs to the current fiber. The
// work-in-progress hook list is a new list that will be added to the
// work-in-progress fiber. 
```

Enter fullscreen mode Exit fullscreen mode

如果你不知道什么是纤维，不要担心。它们基本上是 React 中一个工作单元的代码表示。从最初的[光纤 github](https://github.com/acdlite/react-fiber-architecture#what-is-a-fiber) :

> *   Pause your work and come back later.
> *   Distinguish jobs.
> 
> *   Suspend work that is no longer needed.

因此，钩子存储在纤程的状态中。纤程有一个当前钩子的链表。当 React 中有更新时，纤程会创建一个新的正在进行的挂钩列表，并将其附加到列表中。

下面是来自[React src](https://github.com/facebook/react/blob/master/packages/react-reconciler/src/ReactFiberHooks.js#L264)的钩子的实现(注释来自代码本身):

```
function createWorkInProgressHook(): Hook {
  if (workInProgressHook === null) {
    // This is the first hook in the list
    if (firstWorkInProgressHook === null) {
      isReRender = false;
      currentHook = firstCurrentHook;
      if (currentHook === null) {
        // This is a newly mounted hook
        workInProgressHook = createHook();
      } else {
        // Clone the current hook.
        workInProgressHook = cloneHook(currentHook);
      }
      firstWorkInProgressHook = workInProgressHook;
    } else {
      // There's already a work-in-progress. Reuse it.
      isReRender = true;
      currentHook = firstCurrentHook;
      workInProgressHook = firstWorkInProgressHook;
    }
  } else {
    if (workInProgressHook.next === null) {
      isReRender = false;
      let hook;
      if (currentHook === null) {
        // This is a newly mounted hook
        hook = createHook();
      } else {
        currentHook = currentHook.next;
        if (currentHook === null) {
          // This is a newly mounted hook
          hook = createHook();
        } else {
          // Clone the current hook.
          hook = cloneHook(currentHook);
        }
      }
      // Append to the end of the list
      workInProgressHook = workInProgressHook.next = hook;
    } else {
      // There's already a work-in-progress. Reuse it.
      isReRender = true;
      workInProgressHook = workInProgressHook.next;
      currentHook = currentHook !== null ? currentHook.next : null;
    }
  }
  return workInProgressHook;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们稍微深入一下:

```
if (workInProgressHook === null) { 
```

Enter fullscreen mode Exit fullscreen mode

正在检查是否有当前正在使用的钩子。`workInProgressHook`全局变量，用法如下:

```
workInProgressHook = createWorkInProgressHook(); 
```

Enter fullscreen mode Exit fullscreen mode

那么，如果`workInProgressHook`为空，我们要做什么呢？

空的 workInProgressHook 意味着我们当前没有构建钩子。这意味着我们希望使用当前的*钩子，而不是构建列表中的下一个钩子。有两种情况下我们可能需要这样做——当我们构建一个新的列表并制作第一个元素时，以及当我们重新加载一个现有的列表时。接下来的`if`语句向我们展示了这一点(评论甚至很好地告诉了我们！):* 

```
// This is the first hook in the list
if (firstWorkInProgressHook === null) {
  isReRender = false;
  currentHook = firstCurrentHook;
  if (currentHook === null) {
    // This is a newly mounted hook
     workInProgressHook = createHook();
  } else {
    // Clone the current hook.
    workInProgressHook = cloneHook(currentHook);
  }
  firstWorkInProgressHook = workInProgressHook;
} else {
  // There's already a work-in-progress. Reuse it.
  isReRender = true;
  currentHook = firstCurrentHook;
  workInProgressHook = firstWorkInProgressHook;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们没有`firstWorkInProgressHook`，我们需要开始构建链表——它还有另一个 If 语句。但是，如果我们确实有一个，这意味着我们已经在创建一个正在进行的工作，并且第一个当前钩子可以被复制！

下一条 if 语句下来:

```
if (currentHook === null) {
  // This is a newly mounted hook
   workInProgressHook = createHook();
} else {
  // Clone the current hook.
  workInProgressHook = cloneHook(currentHook);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们要检查我们是否有一个当前的钩子。如果我们不这样做，我们将创建一个全新的列表，这意味着我们调用`createHook`。

> `createHook`只是返回一个带有一堆空集属性的对象。我们会对`next`地产感兴趣的那家。

否则，这是一个我们已经渲染过一次的列表。React 通过克隆钩子并继续前进来节省一些性能，这样它就不必在每次调用这个函数时重新创建一个钩子。

所以现在我们有了第一个钩子！我们把我们的`firstWorkInProgressHook`设置为我们刚刚做的新的！

```
firstWorkInProgressHook = workInProgressHook; 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们再次调用`createWorkInProgressHook`时会发生什么？

```
function createWorkInProgressHook(): Hook {
  if (workInProgressHook === null) {
    // ...
  } else {
    // what happens here?
  }
  return workInProgressHook;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看！

既然`workInProgressHook`不为空，我们需要检查它是否有下一个钩子-

```
if (workInProgressHook.next === null) { 
```

Enter fullscreen mode Exit fullscreen mode

如果没有，我们应该创建一个新的钩子并把它添加到列表中，这就是将要发生的事情:

```
isReRender = false;
let hook;
if (currentHook === null) {
  // This is a newly mounted hook
  hook = createHook();
} else {
  currentHook = currentHook.next;
  if (currentHook === null) {
    // This is a newly mounted hook
    hook = createHook();
  } else {
    // Clone the current hook.
    hook = cloneHook(currentHook);
  }
}
// Append to the end of the list
workInProgressHook = workInProgressHook.next = hook; 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们再次检查当前是否有钩子要克隆(这次检查下一个钩子是否存在，因为这让我们知道这个钩子以前是否被创建过)。如果有，我们克隆它，如果没有，我们创建一个新的。

然后我们把这条神奇的线叫做:

```
workInProgressHook = workInProgressHook.next = hook; 
```

Enter fullscreen mode Exit fullscreen mode

它获取我们新创建的钩子，将其设置为当前钩子的下一个钩子，然后将当前钩子设置为新钩子。这个基本相当于这个:

```
workInProgressHook.next = hook;
workInProgressHook = hook; 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果我们已经有了一个正在进行的钩子，我们就重新提交，并且想做和第一次一样的事情:

```
} else {
  // There's already a work-in-progress. Reuse it.
  isReRender = true;
  workInProgressHook = workInProgressHook.next;
  currentHook = currentHook !== null ? currentHook.next : null;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一的区别是我们需要在移动一个钩子之前更新 currentHook 为下一个钩子，这是用最后一行的三元组完成的。

我们用一些全局变量和一个函数创建了一个链表！那很酷！

老实说，这可能是我第一次在实际代码中看到链表！希望这篇文章有意义！如果有任何问题，请告诉我！