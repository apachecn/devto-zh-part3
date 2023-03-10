# 在 JavaScript 中使用代理扩展数组

> 原文：<https://dev.to/michi/extending-arrays-using-proxies-in-javascript-b8m>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/extending-arrays-using-proxies)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

在上一篇文章中，我们看到了数组的子类化。这次我们来看看 ES6 带来的另一个特性。代理！

我们将继续本着测试驱动开发的精神。如果你没有看过
之前的帖子，我们基本上安装了测试用的摩卡和柴，有一个`src`和一个`test`文件夹。

目标是在我们的数组上有动态的方法名，这样我们基本上可以集成任何库。

我来给你介绍一下`roxy`。你可以在这里找到 GitHub [。](https://github.com/MZanggl/roxy)

在我解释什么是代理之前，让我们看一下测试，这样你就能更好地了解我们想要达到的目标。

> 请注意，由于代理的性质，它们是不可聚合的。

在上一篇文章中，我们首先整合了 pluck 方法。如果你对它不熟悉，这里是 ES6 的做法

```
const locations = [
  { city: 'Tokyo' },
  { city: 'Naha' },
]

// pluck in ES6
const cities = locations.map(loc => loc.city)
cities //? [ 'Tokyo', 'Naha'] 
```

Enter fullscreen mode Exit fullscreen mode

目标是把`locations.map(loc => loc.city)`变成`locations.pluck('city')`。

为了帮助我们实现目标，同时添加许多其他有用的方法，让我们集成 lodash。

为了使用 lodash 拔毛，你使用 lodash 自己的`map`方法。如果您传递一个字符串而不是一个函数作为第二个参数，该字符串将成为它要选取的键。

```
_.map(locations, 'city') 
```

Enter fullscreen mode Exit fullscreen mode

这是测试结果

```
const expect = require('chai').expect
const { proxy } = require('../src/roxy')

describe('proxy', function() {
    it('should be able to access Array.prototype methods', function() {
        const numbers = proxy([1, 2, 3])
        numbers.copyWithin(0, 1, 2) // lodash doesn't have `copyWithin`
        expect(numbers).to.deep.equal([ 2, 2, 3 ])
    })

    it('should pluck using lodash method', function() {
        const numbers = proxy([
            { id: 1 },
            { id: 2 },
            { id: 3 },
        ])

        const result = numbers.map('id')
        expect(result).to.deep.equal([ 1, 2, 3 ])
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

那很酷。我们完全去掉了`new`关键字和类。我们来看看`roxy.js`。

```
// let's only load the lodash methods we need
var lodashArray = require('lodash/array')
var lodashCollection = require('lodash/collection')

const proxySymbol = Symbol('isProxy')

function _transformResult(result) {
    if (Array.isArray(result) && !result[proxySymbol]) {
        return proxy(result)
    }
    return result
}

const libraryList = [lodashCollection, lodashArray]

function proxy(array) {
    const handler = {
        get: function get(target, prop) {
            const library = libraryList.find(library => typeof library[prop] === 'function')
            if (library) {
                return function(...args) {
                    const result = library[prop](this, ...args)
                    return _transformResult(result)
                }
            }

            if (typeof target[prop] !== 'undefined') {
                return target[prop]
            }

            if (prop === proxySymbol) {
                return true
            }
        }
    }

    return new Proxy(array, handler)
}

module.exports = {
    proxy,
} 
```

Enter fullscreen mode Exit fullscreen mode

代理顾名思义。我们也可以说它是实际阵列的网关。但是这个入口由所谓的`traps`守卫着。在这种情况下，无论何时访问数组上的一个属性，它都不会实际访问它，但是它会在我们的处理程序的`get`方法中获得`trapped`，所以让我们一步一步来理解它。

```
const library = libraryList.find(library => typeof library[prop] === 'function')
if (library) {
    return function(...args) {
        const result = library[prop](this, ...args)
        return _transformResult(result)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们检查该方法是在 lodash 集合中还是在 lodash 数组中。

如果你访问`array.map`，它将在`lodashCollection`中找到它并返回一个新函数。该函数已经知道此时`library[prop]`将是`lodashCollection.map`。

然后，当您像这样执行函数`array.map('id')`时，它将接受您传递的参数，并执行 lodash 函数以及作为第一个参数的实际数组。

用`_transformResult`我们将再次代理结果，以防它是一个普通数组。这允许更好的链接。

* * *

```
if (typeof target[prop] !== 'undefined') {
    return target[prop]
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们要检查该方法是否是数组的现有属性，并简单地返回它。访问 lodash 中不存在的属性`length`或方法`copyWithin`就是这种情况。

```
if (prop === proxySymbol) {
    return true
} 
```

Enter fullscreen mode Exit fullscreen mode

这让我们知道数组是否是一个 roxy 实例。在`_transformResult`中，当我们访问`result[proxySymbol]`并且`result`已经是一个 roxy 实例时，它会被`get`方法捕获，并在此时`if (prop === proxySymbol)`返回 true。因此，如果返回的数组已经是一个 roxy 实例，就不需要再次代理它。

再次检查`_transformResult`的部分:

```
if (Array.isArray(result) && !result[proxySymbol]) {
    return proxy(result)
}
return result 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过编写另一个测试
来检查`_transformResult`是否有效

```
it('should be able to chain lodash methods', function() {
    const locations = proxy([
        { location: {city: 1 } },
        { location: {city: 2 } },
        { location: {city: 3 } },
    ])

    const result = locations.map('location').map('city')
    expect(result).to.deep.equal([ 1, 2, 3 ])
}) 
```

Enter fullscreen mode Exit fullscreen mode

同样，你现在可以使用洛达什的`map`方法，你应该可以使用一堆更像`chunk`、`keyBy`、`shuffle`等等。

当然不一定要用`lodash`。您可以对任何数组库做类似的事情。但是可能没有一个图书馆能满足你所有的期望。因此，让我们也添加一个方法来创建自定义宏。

测试

```
const { proxy, macro } = require('../src/roxy')

// ...

it('can use macros', function() {
    macro('stringify', (array, prepend) => prepend + JSON.stringify(array))

    const numbers = proxy([1, 2])
    const result = numbers.stringify('array: ')
    expect(result).to.equal('array: [1,2]')
}) 
```

Enter fullscreen mode Exit fullscreen mode

对于实现，我们只需要做几件事情。

```
const macroMap = {}
const libraryList = [lodashCollection, lodashArray, macroMap]

function macro(name, fn) {
    macroMap[name] = fn
}

// ...

module.exports = {
    proxy,
    macro,
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是全部了！

## 结论

代理提供了广泛的新的可能性来探索。事实上，[vue 的下一个主要版本将为其反应系统](https://medium.com/vue-mastery/evan-you-previews-vue-js-3-0-ab063dec3547)使用代理。本教程只使用了`get`陷阱。像`set`、`construct`、`has`等其实还有很多。查看下面的 mdn 参考资料，了解有关代理的更多信息。

## 参考文献

*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
*   [https://github.com/MZanggl/roxy](https://github.com/MZanggl/roxy)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)