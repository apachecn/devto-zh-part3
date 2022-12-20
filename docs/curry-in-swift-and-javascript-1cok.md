# Swift 和 Javascript 中的库里

> 原文：<https://dev.to/onmyway133/curry-in-swift-and-javascript-1cok>

[![](img/69d5fef4148370713af16ff033552152.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sJvb7oya--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2600/0%2AMlDvDUWN7C4AwXA-)

日常代码中可能会遇到库里而不自知。下面是我对 curry 以及如何在 Javascript 和 Swift 中应用它的一点思考。

## 在 Haskell 中取一个参数

在 Haskell 中，所有函数都只接受一个参数。带有许多参数的函数只是被简化，这意味着它们将被部分应用。调用 sum 1 只是返回一个带有 1 个参数的函数，然后将 2 传递给这个函数。下面两个函数调用是一样的。

```
ghci> sum 1 2
3 
ghci> (max 1) 2
3 
```

我倾向于把可定制的功能或部分应用的功能看作是在每个应用步骤中都带有依赖性的东西。每个函数都可以被赋值给变量或传递，或者作为返回值。

## 库里在雨燕中作谓语

当我试图制作我自己的[信号](https://github.com/onmyway133/archives/tree/master/Signal)库时，我已经

和事件

### 滤镜

那么应该有一个信号过滤器。过滤的思想是，如果事件是下一个正确的过滤值，我们应该更新信号

```
public func filter(f: T -> Bool) -> Signal<T>{
    let signal = Signal<T>()
    subscribe { result in
        switch(result) {
        case let .Success(value):
            if f(value) {
                signal.update(result)
            }
        case let .Error(error): signal.update(.Error(error))
        }
    }
    return signal
} 
```

### 2 参数

但是将事件作为另一个单子，我认为如果将切换逻辑移到事件中，它应该被更好地封装。这里的过滤器有两个参数

Event.swift

```
func filter(f: T -> Bool, callback: (Event<T> -> Void)) {
        switch self {
        case let .Next(value) where f(value):
            callback(self)
        case .Failed:
            callback(self)
        default:
            break
    }
} 
```

信号.斯威夫特

```
public func filter(f: T -> Bool) -> Signal<T> {
    let signal = Signal<T>()

    subscribe { event in
        event.filter(f, callback: signal.update)
    }

    return signal
} 
```

### 逢迎

使用 currying，我们可以使 filter 成为一个更抽象的函数，并推迟传递回调参数的决定。这是有点忘乎所以，但我发现这种方式很有帮助

现在 filter 接受 1 个参数，它返回一个以 callback 为参数的函数

Event.swift

```
func filter(f: T -> Bool) -> ((Event<T> -> Void) -> Void) {
        return { g in
            switch self {
            case let .Next(value) where f(value):
                g(self)
            case .Failed:
                g(self)
            default:
                break
            }
        }
    } 
```

信号.斯威夫特

```
public func filter(f: T -> Bool) -> Signal<T> {
        let signal = Signal<T>()

        subscribe { event in
            event.filter(f)(signal.update)
        }

        return signal
    } 
```

### Swift 2 及以上版本中的库里语法

Swift 2 支持库里语法功能

```
func sum(a: Int)(b: Int) -> Int {
    return a + b
}

let sumWith5 = sum(5)
let result = sumWith5(b: 10) 
```

不幸的是，自 Swift 3 以来，声明 curry 的语法糖已经被放弃了。你可能想在[告别阿谀奉承](http://ericasadun.com/2015/12/18/bidding-farewell-to-currying/)中找到答案。但这没什么大不了的，因为我们可以很容易地创建咖喱函数。它只是一个函数返回另一个函数。

## 在 UIKit 中使用 curry 进行部分应用

我在我的 Xkcd 应用程序中使用了这种咖喱技术。参见 [MainController.swift](https://github.com/onmyway133/Xkcd/blob/master/Source/Feature/Main/MainController.swift) 。MainController 是 vanilla UITabBarController，带有 ComicsController 和 FavoriteController，都嵌入在 UINavigationViewController 中。

该功能的特点是，当一个漫画被选中时，一个漫画细节屏幕应该被推到导航堆栈的顶部。例如在[委员会控制器](https://github.com/onmyway133/Xkcd/blob/master/Source/Feature/Comics/ComicsContoller.swift#L20)

```
/// Called when a comic is selected  
var selectComic: ((Comic) -> Void)? 
```

所有 ComicsController 需要知道的是用选择的漫画调用 selectComic 闭包，应该有人知道如何处理这个选择。回到 MainController 内部的 [handleFlow](https://github.com/onmyway133/Xkcd/blob/master/Source/Feature/Main/MainController.swift) 函数。

```
private func handleFlow() {
  typealias Function = (UINavigationController) -> (Comic) -> Void
  let selectComic: Function = { [weak self] navigationController in
    return { (comic: Comic) in
      guard let self = self else {
        return
      }

  let detailController = self.makeDetail(comic: comic)
      navigationController.pushViewController(detailController, animated: true)
    }
  }

  comicsController.selectComic = selectComic(comicNavigationController)
  favoriteController.selectComic = selectComic(favoriteNavigationController)
} 
```

我将 Function 声明为 typealias，以显式声明我们将要构建的 curry 函数

```
typealias Function = (UINavigationController) -> (Comic) -> Void 
```

我们构建 selectComic 作为 curried 函数，它采用 UINavigationViewController 并返回一个采用 Comic 并返回 Void 的函数。这样，当我们用 navigationController 部分应用 selectComic 时，我们会得到另一个函数，它将 navigationController 作为依赖项，并准备好分配给 comicsController 中的 selectComic 属性。

## Javascript 中的库里许诺功能

我喜欢在 Javascript 中使用 Promise 和 async/await。它允许连锁风格和容易的原因。因此，当在 Javascript 中处理回调时，例如 React Native 中来自本机模块的回调，我倾向于将它们转换为 Promise。

例如，当使用 HealthKit 时，我们需要公开它周围的本机模块

```
// [@flow](http://twitter.com/flow)

import { NativeModules } from 'react-native'

type HealthManagerType = {
  checkAuthorisation: ((string) => void)) => void,
  authorise: ((boolean) => void)) => void,
  readWorkout: (Date, Date, () => void)) => void,
  readDailySummary: (Date, Date, () => void)) => void,
  readMeasurement: (Date, Date, () => void)) => void
}

const HealthManager: HealthManagerType = NativeModules.HealthManager
export default HealthManager 
```

我们可以构建一个承诺函数，将带有回调的函数转换成承诺

```
// [@flow](http://twitter.com/flow)

const toPromise = (f: (any) => void) => {
  return new Promise<any>((resolve, reject) => {
    try {
      f((result) => {
        resolve(result)
      })
    } catch (e) {
      reject(e)
    }
  })
}

export default toPromise 
```

然而，正如您在签名中看到的，它只对类型为(any) => void 的回调有效。换句话说，这个回调必须有一个参数，因为 Promise 要么返回值，要么抛出错误。

为了解决这个问题，我们可以构建一个 curry 函数，它可以将带有 1、2、3 个参数的函数转化为 curry 函数。多亏了 Javascript 的动态特性，我们有了

```
// [@flow](http://twitter.com/flow)

function curry0(f: () => void) {
  return f()
}

function curry1(f: (any) => void) {
  return (p1: any) => {
    return f(p1)
  }
}

function curry2(f: (any, any) => void) {
  return (p1: any) => {
    return (p2: any) => {
      return f(p1, p2)
    }
  }
}

function curry3(f: (any, any, any) => void) {
  return (p1: any) => {
    return (p2: any) => {
      return (p3: any) => {
        return f(p1, p2, p3)
      }
    }
  }
}

export default {
  curry0,
  curry1,
  curry2,
  curry3
} 
```

因此，对于一个有 3 个参数的函数，我们可以使用 curry3 来部分应用前 2 个参数。然后我们有一个只接受回调的函数，这个函数通过 toPromise 转化为承诺

```
const readWorkout = curry.curry3(HealthManager.readWorkout)(DateUtils.startDate))(DateUtils.endDate))

const 健身程序= wait to promise(read workout) 
```

## 
  
何去何从

这里有一些我最喜欢的帖子，可以阅读更多关于库里的内容

*   [高阶函数](http://learnyouahaskell.com/higher-order-functions)

*   [Swift 中 Currying 功能介绍](https://robots.thoughtbot.com/introduction-to-function-currying-in-swift)

*   [Swift 函数 curring](http://www.russbishop.net/swift-function-currying)

*   JavaScript 中的 curry:我喜欢他如何使用内存和切片来逐步构建更通用的 curry 函数

原帖[https://medium . com/fantageek/curry-in-swift-and-JavaScript-BCD 1245 b 30d 3](https://medium.com/fantageek/curry-in-swift-and-javascript-bcd1245b30d3)