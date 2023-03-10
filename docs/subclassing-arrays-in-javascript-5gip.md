# JavaScript 中数组的子类化

> 原文：<https://dev.to/michi/subclassing-arrays-in-javascript-5gip>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/subclassing-arrays)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

在我之前的文章中，我展示了如何用不同的数组方法来揭示我们的意图。但最终我对结果并不真正满意。

而

```
const usernames = users.map(user => user.name) 
```

Enter fullscreen mode Exit fullscreen mode

绝对比
可读性强得多

```
const usernames = []

users.forEach(user => {
  usernames.push(user.name)
}) 
```

Enter fullscreen mode Exit fullscreen mode

不会吧

```
const usernames = users.pluck('name') 
```

Enter fullscreen mode Exit fullscreen mode

变得更好？

因此，让我们看看如何创建这样的功能。让我们深入数组子类化的世界。我们还将关注 NodeJS 中的单元测试，以及一种更具功能性的替代方法。

顺便说一句。我不是在这里宣传什么革命性的新图书馆。我们只是在探索想法。我仍然为此创建了一个 [GitHub repo](https://github.com/MZanggl/subclassing-array-experiments) ,所以如果你想的话，你可以检查整个代码。

* * *

### 但是首先，我们如何在 JavaScript 中创建数组？

经典

```
const numbers = [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

还有可能不太为人所知的

```
const numbers = new Array(1, 2, 3) 
```

Enter fullscreen mode Exit fullscreen mode

但是当你只传递一个参数时，上面并没有做你所期望的事情。`new Array(3)`将创建一个有三个空值的数组，而不是一个只有一个值为`3`的数组。

ES6 引入了一个静态方法来修复这种行为。

```
const numbers = Array.of(1, 2, 3) 
```

Enter fullscreen mode Exit fullscreen mode

然后还有这个

```
const array = Array.from({ length: 3 })
//? (3) [undefined, undefined, undefined] 
```

Enter fullscreen mode Exit fullscreen mode

上面的工作是因为`Array.from`期望一个类似数组的对象。创建这样一个对象只需要一个具有长度属性的对象。

它还可以有第二个参数来映射数组。

```
const array = Array.from({ length: 3 }, (val, i) => i)
//? (3) [0, 1, 2] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

考虑到这一点，让我们创建类固醇的`Steray`、**数组。**

随着 ES6 和类的引入，很容易扩展数组

```
class Steray extends Array {
    log() {
        console.log(this)
    }
}

const numbers = new Steray(1, 2, 3)
numbers.log() // logs [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利，但是如果我们有一个现有的数组并想把它变成一个`Steray`呢？

请记住，使用`Array.from`我们可以通过传递一个类似数组的对象来创建一个新数组，数组不也包含在这个定义中吗？

这最终意味着我们可以做到这一点

```
const normalArray = [1, 2, 3]
const steray = Steray.from(normalArray) 
```

Enter fullscreen mode Exit fullscreen mode

或者可选地

```
const normalArray = [1, 2, 3]
const steray = Steray.of(...normalArray) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

让我们开始给`Steray`添加一些方法。
在`steray.js`中，我们可以将期待已久的`pluck`方法添加到
类中

```
pluck(key) {
    return this.map(item => item[key])
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。优雅而有力。

## 设置测试

但是我们怎么知道这行得通呢？我们不知道每次都要进入浏览器，在控制台中测试我们的类。因此，让我们快速设置单元测试，这样我们就可以确信我们所做的是正确的。

创建以下目录结构

```
steray
    src
        steray.js
    test
        sterayTest.js 
```

Enter fullscreen mode Exit fullscreen mode

安装节点和 npm 后，全局安装单元测试框架`mocha`。

```
npm install mocha -g 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们通过在根目录下运行`npm init`来初始化`package.json`。按照指示，直到它创建一个`package.json`文件。当它要求你输入`test`脚本时，输入`mocha`。或者，您也可以稍后在`package.json`中对此进行更改。

```
"scripts":  {  "test":  "mocha"  }, 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在本地安装断言库`chai`

```
npm install chai --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们要做的一切。让我们打开`sterayTest.js`并编写我们的第一个测试

```
const expect = require('chai').expect
const Steray = require('../src/steray')

describe('pluck', function() {
    it('should pluck values using the "name" prop', function() {
        const users = new Steray( 
            { name: 'Michael' },
            { name: 'Lukas' },
        )

        const names = users.pluck('name')
        expect(names).to.deep.equal([ 'Michael', 'Lukas' ])
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

使用目录根目录中的`npm run test`运行测试，它应该输出一个测试通过。
这样一来，我们现在可以安全地继续编写新方法，或者更改`pluck`的实现，而不必担心代码被破坏。

让我们添加更多的方法，但是这次是本着测试驱动开发的精神！

* * *

你知道我最不喜欢什么吗？这些讨厌的循环。

```
for (let i; i < 10; i++) 
```

Enter fullscreen mode Exit fullscreen mode

是`let i`还是`const i`，是`<`还是`<=`？如果有更简单的方法来实现这一点不是很好吗？虽然你可以使用我们之前学过的语法`Array.from({ length: 10 }, (value, index) => index)`,但这是不必要的冗长。
受 lodash 和 Laravel 系列的启发，让我们创建一个静态`times`方法。

为了让您看到该方法的运行，让我们首先创建单元测试。

```
describe('times', function() {
    it('should return an array containing the indices 0 and 1', function() {
        const numbers = Steray.times(2, i => i)
        expect(numbers).to.deep.equal([ 0, 1 ])
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

尝试运行`npm run test`，它应该会返回错误，因为`times`还不存在。

> 我将总是首先显示测试，所以在查看我的实现之前，您可以尝试自己实现该方法。找到了更好的解决方案？发个 PR！

所以，这里是我在`steray.js`
中实现的`times`

```
static times(length, fn) {
    return this.from({ length }, (value, i) => fn(i))
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

有时，如果有一个很长的链，您可能会感到困惑，并且您想要进入流程以查看发生了什么。所以让我们来构建这个功能。

一个示例用例是

```
[1, 2, 3, 4, 5]
    .filter(i => i < 4)
    .map(i => i * 10)
    .tap(console.log)
    .find(i => i === 20) 
```

Enter fullscreen mode Exit fullscreen mode

执行该函数，但随后只是再次返回完全相同的数组，没有修改。`tap`不返回回调返回的内容。
对于这样的功能，让我们创建另一个方法`pipe`。

以下是测试

```
describe('tapping and piping', function() {
    it('should execute callback one time', function() {
        let i = 0
        new Steray(1, 2, 3).tap(array => i = i + 1)

        expect(i).to.equal(1)
    })

    it('should return original array when tapping', function() {
        const array = new Steray(1, 2, 3).tap(() => 10)
        expect(array).to.deep.equal([1, 2, 3])
    })

    it('should return result of pipe', function() {
        const piped = new Steray(1, 2, 3).pipe(array => array.length)
        expect(piped).to.equal(3)
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里是实现

```
tap(fn) {
    fn(this)
    return this
} 
```

Enter fullscreen mode Exit fullscreen mode

```
pipe(fn) {
    return fn(this)
} 
```

Enter fullscreen mode Exit fullscreen mode

令人惊讶的是这些方法是如此的小而强大！

* * *

还记得在之前的博客文章中，我们是如何将`users`数组转换成按`group`键分组的散列表的吗？

让我们通过实现一个新方法`groupBy`来创建这个功能！下面是测试

```
describe('groupBy', function() {
    it('should hashMap', function() {
        const users = new Steray( 
            { name: 'Michael', group: 1 },
            { name: 'Lukas', group: 1 },
            { name: 'Travis', group: 2 },
        )

        const userMap = users.groupBy('group')

        expect(userMap).to.deep.equal({
            '1': [
                { name: 'Michael', group: 1 },
                { name: 'Lukas', group: 1 },
            ],
            '2': [
                { name: 'Travis', group: 2 },
            ]
        })
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里是实现

```
groupBy(groupByProp) {
    return this.reduce((result, item) => {
        const id = item[groupByProp]
        result[id] = result[id] || new []

        result[id].push(rest);

        return result;
    }, {})
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这样做可行，但我们可能会在某一点上遇到问题。我将添加另一个单元测试来说明什么会出错。

```
it('should hashMap using Steray array', function() {
    const users = new Steray( 
        { name: 'Michael', group: 1 },
        { name: 'Lukas', group: 1 },
        { name: 'Travis', group: 2 },
    )

    const userMap = users.groupBy('group')
    const groupOne = userMap['1']
    const isInstanceOfSteray = (groupOne instanceof Steray)
    expect(isInstanceOfSteray).to.be.true
}) 
```

Enter fullscreen mode Exit fullscreen mode

出问题的是`result[id] = result[id] || []`，具体是`[]`。因为我们创建了一个普通数组，所以我们所有新实现的方法都不可用。

为了解决这个问题，让我们使用`result[id] = result[id] || new Steray`来代替。

虽然测试会通过，但解决方案也不是 100%干净的。
如果我们后来想把这个函数移到自己的文件里，导入到这里，岂不是会产生循环依赖？同样，如果它不知道`Steray`就好了。

在我看来，更好的解决方案是下面的

```
result[id] = result[id] || new this.constructor 
```

Enter fullscreen mode Exit fullscreen mode

`this`指的是立体数组，使用`this.constructor`我们可以动态地得到类`Steray`。

* * *

我们真的可以添加很多东西

*   重复数据删除
*   组块
*   填料
*   将数据添加到数组中，而不转换原始数组(与`unshift`不同)

仅举几个例子。

你可以在下面的 [GitHub repo](https://github.com/MZanggl/subclassing-array-experiments) 中找到包含单元测试的`Steray`类以及上面提到的`chunk`、`pad`、`unique`、`prepend`等方法。

## 子类化的替代方案

最终，我们的类可能会成长为一大堆杂乱的帮助函数，你可能会遇到某些限制。

另一种不同的方法是让 [ramda](https://ramdajs.com/) 完全发挥作用。
Ramda 还有一个额外的好处，它还有对象、字符串、数字甚至函数的方法。

拉姆达的一个例子是

```
const R = require('ramda')

const users = [
    { name: 'Conan', location: { city: 'Tokyo' } },
    { name: 'Genta', location: { city: 'Tokyo' } },
    { name: 'Ayumi', location: { city: 'Kawasaki' } },
]

const getUniqueCitiesCapitalized = R.pipe(
    R.pluck('location'),
    R.pluck('city'),
    R.map(city => city.toUpperCase()),
    R.uniq()
)
const cities = getUniqueCitiesCapitalized(users)

expect(cities).to.deep.equal(['TOKYO', 'KAWASAKI']) 
```

Enter fullscreen mode Exit fullscreen mode

那么我们把两者结合起来怎么样，一个简单的数组子类，具有消费 ramda 函数的能力。我知道我知道，我们在这一点上有点滥用 ramda，但检查一下仍然很有趣。我们只需要一个新名字...，我们的数组类不再是真正的类固醇了，它完全相反，所以让我们称它为`Yaseta`，当有人减肥时的日语表达。

让我们使用`npm install ramda --save-dev`安装 ramda(我们只在测试中需要它)并创建一些测试，这样我们就可以看到我们将如何使用我们的新库。

```
// test/yasetaTest.js

const expect = require('chai').expect
const Yaseta = require('../src/yaseta')
const pluck = require('ramda/src/pluck')

describe('underscore methods', function() {
    it('returns result of callback', function() {
        const numbers = new Yaseta(1, 2)
        const size = numbers._(array => array.length)

        expect(size).to.equal(2)
    })

    it('returns result of assigned callback using higher order function', function() {
        const users = new Yaseta(
            { name: 'Conan' },
            { name: 'Genta' }
        )

        // this is how ramda works
        const customPluck = key => array => {
            return array.map(item => item[key])
        }

        const usernames = users._(customPluck('name'))

        expect(usernames).to.deep.equal(['Conan', 'Genta'])
    })

    it('can assign ramda methods', function() {
        const users = new Yaseta(
            { name: 'Conan' },
            { name: 'Genta' }
        )

        const usernames = users._(pluck('name'))

        expect(usernames).to.deep.equal(['Conan', 'Genta'])
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

让我们在`src`目录中创建`yaseta.js`。

```
class Yaseta extends Array {
    _(fn) {
        const result = fn(this)
        return this._transformResult(result)
    }

    _transformResult(result) {
        if (Array.isArray(result)) {
            return this.constructor.from(result)
        }

        return result
    }
}

module.exports = Steray 
```

Enter fullscreen mode Exit fullscreen mode

我们将该方法称为`_`,通过仍然提供一些可读性(至少对于熟悉 lodash 等的人来说)来占用最少的空间。不管怎样，我们只是在探索想法。

但是`_transformResult`是怎么回事？

看看当`ramda`创建新数组时，它并没有使用`array.constructor`来完成。它只是创建了一个普通的数组，我猜这是因为他们的`list`函数也适用于其他的可迭代对象。所以我们不能说

```
numbers
    ._(array => array)
    ._(array => array) // would crash here 
```

Enter fullscreen mode Exit fullscreen mode

但是由于有了`_transformResult`，我们又把它变成了`Yaseta`实例。让我们添加另一个测试，看看上述是否可能

```
const pluck = require('ramda/src/pluck')
const uniq = require('ramda/src/uniq')
const map = require('ramda/src/map')
// ...
it('can chain methods with ramda', function() {
    const users = new Yaseta(
        { name: 'Conan', location: { city: 'Tokyo' } },
        { name: 'Genta', location: { city: 'Tokyo' } },
        { name: 'Ayumi', location: { city: 'Kanagawa' } },
    )

    const cities = users
        ._(pluck('location'))
        ._(pluck('city'))
        .map(city => city.toUpperCase())
        ._(map(city => city.toUpperCase())) // same as above
        .filter(city => city.startsWith('T'))
        ._(uniq)

        expect(cities).to.deep.equal(['TOKYO'])
}) 
```

Enter fullscreen mode Exit fullscreen mode

让我们也创建一个`pipe`方法。这一次，您可以根据需要传递任意多的函数。

```
describe('pipe', function() {
    it('can pipe methods', function() {
        const users = new Yaseta(
            { name: 'Conan', location: { city: 'Tokyo' } },
            { name: 'Genta', location: { city: 'Tokyo' } },
            { name: 'Ayumi', location: { city: 'Kanagawa' } },
        )

        const cities = users
            .pipe(
                pluck('location'),
                pluck('city'),
                map(city => city.toUpperCase())
            )
            .filter(city => city.startsWith('T'))
            ._(uniq)

            expect(cities).to.deep.equal(['TOKYO'])
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

以及 Yaseta 类中的实现:

```
pipe(...fns) {
    const result = fns.reduce((result, fn) => {
        return fn(result)
    }, this)

    return this._transformResult(result)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

那么当我们比较不同的解决方案时，我们现在有什么？

**球面度**

```
users = Steray.from(users)
const usernames = users.pluck('name') 
```

Enter fullscreen mode Exit fullscreen mode

**插入**

```
users = Yaseta.from(users)
const usernames = users._(pluck('name')) 
```

Enter fullscreen mode Exit fullscreen mode

**拉姆达**

```
const usernames = R.pluck('name')(users) 
```

Enter fullscreen mode Exit fullscreen mode

**香草**T2】

```
const usernames = users.map(user => user.name) 
```

Enter fullscreen mode Exit fullscreen mode

各有各的好处

**星期三**

[+]超级可读

[-]需要对数组进行子类化

[-]手动定义类的方法

**插入**

[+]可以使用所有的 ramdas 方法，但不限于 ramda

[+] OSS 贡献者也可以添加更多你可以单独安装的功能。

[-]需要对数组进行子类化

[-]下划线可能会引起一些误解

**拉姆达**

[+]提供 100%的功能方法

[-]我们不能再使用点符号和`Array.prototype`方法

**香草**

[+]可以在任何地方使用

[+]开发人员无需额外学习

[-]限于现有方法

* * *

在大多数情况下，普通版本可能已经足够好了，但是看看 JavaScript 能做些什么还是很有趣的。

事实证明，实际上还有另一种处理这种事情的方法。如果我们的数组中只有动态方法名，那不是很好吗？事实证明我们可以！

但那是下次的事了。)