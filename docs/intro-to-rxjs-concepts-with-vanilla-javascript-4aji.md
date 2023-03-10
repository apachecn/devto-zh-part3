# 用普通 JavaScript 介绍 RxJS 概念

> 原文：<https://dev.to/creeland/intro-to-rxjs-concepts-with-vanilla-javascript-4aji>

最近的一次网上研讨会启发了我，egghead.io 和 T2 一起主持了这次研讨会，我想分享一下我学到的东西。在网上研讨会之前，我不熟悉 RxJS，这是我第一次接触观察者模式。在它被分解之前，观察者似乎有魔力。

JavaScript 有多个使用回调函数的 API，这些 API 做几乎相同的事情，只是略有不同。

**溪流**

```
stream.on('data', data => {
   console.log(data)
})
stream.on('end', () => {
   console.log("Finished")
})
stream.on('error', err => {
   console.error(err)
}) 
```

Enter fullscreen mode Exit fullscreen mode

**承诺**

```
somePromise()
  .then(data => console.log(data))
  .catch(err => console.error(err)) 
```

Enter fullscreen mode Exit fullscreen mode

**事件监听器**

```
document.addEventListener('click', event => {
  console.log(event.clientX)
}) 
```

Enter fullscreen mode Exit fullscreen mode

你看到的大致模式是，有一个对象，在对象内部，你有一些方法接受一个函数，换句话说，一个回调。它们都在解决同一个问题，但方式不同，这导致您必须承担记住每个 API 的特定语法的精神负担。这就是 RxJS 的用武之地。RxJS 将所有这些统一在一个公共抽象下。

那么什么是可观测的呢？这是一种抽象，就像数组、函数或对象都是抽象一样。承诺可以解决也可以拒绝，给你一个价值。一个可观察对象能够随着时间的推移而释放出价值。您可以使用来自服务器的数据流或监听 DOM 事件。

**💀可观察的骨架**

```
const observable = {
  subscribe: observer => {

  },
  pipe: operator => {

  },
} 
```

Enter fullscreen mode Exit fullscreen mode

可观察对象只是包含一个`subscribe`和`pipe`方法的对象。等等，这是怎么回事？什么是观察者，或者算符？观察者只是包含了`next`、`error`和`complete`的回调方法的对象。`subscribe`方法使用一个观察器并将值传递给它。所以可观察是充当生产者，观察者是它的消费者。

**👀**观察者

```
const observer = {
  next: x => {
    console.log(x)
  },
  error: err => {
    console.log(err)
  },
  complete: () => {
    console.log("done")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个`subscribe`方法中，您将某种形式的数据传递给观察者的方法。

**订阅方法**

```
const observable = {
  subscribe: observer => {
    document.addEventListener("click", event => {
      observer.next(event.clientX)
    })
  },
  pipe: operator => {

  },
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们只是监听文档中任何地方的点击。如果我们运行这个程序并调用`observable.subscribe(observer)`，我们会看到你点击的 x 坐标显示在控制台上。那么这个`pipe`方法呢？`pipe`方法使用一个操作符并返回一个函数，并用可观察对象调用结果函数。

**管法**

```
const observable = {
  subscribe: observer => {
    document.addEventListener("click", event => {
      observer.next(event.clientX)
    })
  },
  pipe: operator => {
    return operator(this)
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

酷，但什么是运营商？运算符用于转换数据。数组有操作符，像`map`。`map`让您后退一步，对数组中的所有内容运行一些函数。你可以有一个数组，然后是另一个数组，它是第一个数组的映射版本。

让我们为我们的可观测值写一个`map`函数。

**🗺️地图操作员**

```
const map = f => {
  return observable => {
    subscribe: observer => {
      observable.subscribe({
        next: x => {
          observer.next(f(x))
        },
        error: err => {
          console.error(err)
        },
        complete: () => {
          console.log("finished")
        }
      })
    },
    pipe: operator => {
      return operator(this)
    },
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了很多事情，让我们来分析一下。

```
const map = f => {
  return observable => { 
```

Enter fullscreen mode Exit fullscreen mode

这里我们传入一个函数并返回一个期望可观测值的函数。还记得我们的`pipe`方法吗？

```
pipe: operator => {
  return operator(this)
}, 
```

Enter fullscreen mode Exit fullscreen mode

为了在可观察对象上运行操作符，需要将它传递给`pipe`。`pipe`将把它所调用的可观察值传递给我们的运算符返回的函数。

```
subscribe: observer => {
  observable.subscribe({ 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们为返回的可观察值定义`subscribe`方法。它期待一个观察器，当`.subscribe`在返回的可观察值上被调用时，无论是通过另一个操作符还是显式地，它都会收到这个观察器。然后，用一个观察者对象调用`observable.subscribe`。

```
{
  next: x => {
    observer.next(f(x))
  },
  error: err => {
    console.error(err)
  },
  complete: () => {
    console.log("finished")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在观察者的`next`方法中，您可以看到，对未来观察者的`next`的调用是用我们最初传递给`map`的函数和传递给`next`的`x`值进行的。让我们在我们的可观察对象上运行新的`map`操作符！

```
observable
  .pipe(map(e => e.clientX))
  .pipe(map(x => x - 1000))
  .subscribe(observer) 
```

Enter fullscreen mode Exit fullscreen mode

需要 final `subscribe`，否则这些操作符中的操作都不会执行，因为它们都被包装在观察者的`subscribe`方法中。在那些`subscribe`方法中，是对链中前一个观察者`subscribe`的调用，但是链必须从某个地方开始。

让我们看看运行时会发生什么。

1.  第一个管道在 observable 上被调用，`map`在`this`上被执行
2.  用`e => e.clientX`调用`map`并返回一个函数
3.  使用原始的`observable`调用该函数，并返回一个可观察值
    1.  我们称之为可观测的
4.  `pipe`被叫上`observable2`和咖喱`map`与`this`
5.  用`x => x - 1000`调用`map`并返回一个函数
6.  用`observable2`调用该函数，并返回一个可观察值
    1.  我们称之为可观测的
7.  `.subscribe`被调用`observable3`并传入一个观察者
8.  `.subscribe`在`observable2`上被调用，操作员的观察者被传入
9.  `.subscribe`在原始可观察值上调用带有操作员的观察者传入
10.  点击事件以`100`的`clientX`发生
11.  `observer2.next(100)`接到电话
12.  `observer3.next(100)`接到电话
13.  `observer.next(-900)`被调用并将`-900`记录到控制台。
14.  搞定了。

你可以看到这里发生的连锁反应。当您调用`subscribe`来请求信息时，每个链接都向链中的前一个链接请求信息，直到它到达数据，并且来自其观察者的`next`方法被调用。然后，这些数据沿着这条链向上传递，一路转换，直到到达最终的观察者。

这是完整的代码。

```
const observable = {
  subscribe: observer => {
    document.addEventListener("click", event => {
      observer.next(event.clientX)
    })
  },
  pipe: operator => {
    return operator(this)
  }
}

const observer = {
  next: x => {
    console.log(x)
  },
  error: err => {
    console.log(err)
  },
  complete: () => {
    console.log("done")
  }
}

const map = f => {
  return observable => {
    subscribe: observer => {
      observable.subscribe({
        next: x => {
          observer.next(f(x))
        },
        error: err => {
          console.error(err)
        },
        complete: () => {
          console.log("finished")
        }
      })
    },
    pipe: operator => {
      return operator(this)
    },
  }
} 
```

Enter fullscreen mode Exit fullscreen mode