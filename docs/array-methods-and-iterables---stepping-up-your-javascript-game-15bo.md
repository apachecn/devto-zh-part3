# 数组方法和 iterables——加强您的 JavaScript 游戏

> 原文：<https://dev.to/michi/array-methods-and-iterables---stepping-up-your-javascript-game-15bo>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/array-methods-and-iterables)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

今天我想介绍一些数组方法，帮助你提升你的 JavaScript 游戏。

对于所有的例子，让我们假设我们有下面的变量声明

```
let users = [
  {id: 1, name: 'Michael', active: true, group: 1 }, 
  {id: 2, name: 'Lukas', active: false, group: 2 }
] 
```

Enter fullscreen mode Exit fullscreen mode

通过这篇文章，你会明白如何把这个

```
const activeUsernames = []

users.forEach(user => {
  if (user.active) {
    activeUsernames.push(user.name)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

变成这样

```
const activeUsernames = users
  .filter(user => user.active)
  .map(user => user.name) 
```

Enter fullscreen mode Exit fullscreen mode

还有更多。

谈到代码改进，我们希望关注四个目标

*   避免临时变量
*   避免条件句
*   能够一步一步地思考你的代码
*   揭示意图

我们将重点介绍数组原型中最重要的方法(忽略基本的数组操作，如`push`、`pop`、`splice`或`concat`)，希望您能找到应用这些方法的场景，而不是下面这些常见的问题。

**为循环**

```
for (let i = 0; i < users.length; i++) {
    //
} 
```

Enter fullscreen mode Exit fullscreen mode

**array . prototype . foreach**T2】

```
users.forEach(function(user) {
    //
} 
```

Enter fullscreen mode Exit fullscreen mode

**ES6 for of Loop**

```
for (const user of users) {
    //
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

在我们开始之前还有一件事！

如果您不熟悉 ES6 箭头功能，如:

```
users.map(user => user.name) 
```

Enter fullscreen mode Exit fullscreen mode

我建议你先看看那些。
综上所述，以上非常相似，在这种情况下，与
相同

```
users.map(function(user) {
   return user.name
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## [T1】array . prototype . filter](#arrayprototypefilter)

假设我们想要找到所有活跃的用户。我们在文章的引言中简要地看了一下这一点。

```
const activeUsers = []

users.forEach(user => {
  if (user.active) {
    activeUsers.push(user)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们回顾一下我们以前确定的四个目标，很明显，这违反了其中至少两个目标。它既有**临时变量**，也有**条件变量**。

让我们看看如何让这变得更容易。

```
const activeUsers = users.filter(user => user.active) 
```

Enter fullscreen mode Exit fullscreen mode

`Array.prototype.filter`的工作方式是将一个函数作为参数(这使得它是一个高阶函数)并返回所有通过测试的用户。在这种情况下是所有活动的用户。

我认为可以肯定地说，我们也能够透露我们的意图。`forEach`可以表示任何东西，可能会保存到数据库等。而`filter`顾名思义。

当然你也可以在一个简单的数组上使用`filter`。
以下示例将返回以字母 a 开头的所有动物。

```
['ape', 'ant', 'giraffe'].filter(animal => animal.startsWith('a')) 
```

Enter fullscreen mode Exit fullscreen mode

我经常看到的一个用例是从数组中移除项目。假设我们删除了 id 为 1 的用户。我们可以这样做

```
users = users.filter(user => user.id !== 1) 
```

Enter fullscreen mode Exit fullscreen mode

过滤器的另一个用途如下

```
const result = [true, 1, 0, false, '', 'hi'].filter(Boolean) 
result //? [true, 1, 'hi'] 
```

Enter fullscreen mode Exit fullscreen mode

这有效地从数组中删除了所有虚假值。这里没有魔法。`Boolean`是一个函数，它接受一个参数来测试它是否为真。例如`Boolean('')`返回 false，而`Boolean('hi')`返回 true。我们简单地将函数传递给`filter`方法，因此它充当我们的测试。

## Array.prototype.map

经常发生的情况是，我们有一个数组，并希望转换其中的每一项。我们可以简单地映射它，而不是遍历它。Map 返回具有相同长度项目的数组，每次迭代返回什么由您决定。

让我们创建一个数组来保存所有用户的用户名。

传统循环

```
const usernames = []

users.forEach(user => {
  usernames.push(user.name)
}) 
```

Enter fullscreen mode Exit fullscreen mode

映射它

```
const usernames = users.map(user => user.name) 
```

Enter fullscreen mode Exit fullscreen mode

我们避免*临时变量*和*同时揭示意图*。

## 链接

这些高阶函数的伟大之处在于它们可以链接在一起。`map`映射一个数组并返回一个新的数组。过滤一个数组并返回一个新的数组。你能看出一种模式吗？考虑到这一点，像下面这样的代码不仅可行，而且非常易读

```
const activeUsernames = users
  .filter(user => user.active)
  .map(user => user.name) 
```

Enter fullscreen mode Exit fullscreen mode

这样我们就完成了最终目标`to think in steps`。你可以一步一步地去做，而不是在脑子里想出整个逻辑。想想我们一开始举的例子。

```
const activeUsernames = []

users.forEach(user => {
  if (user.active) {
    activeUsernames.push(user.name)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

当你第一次读到这篇文章时，你的脑海中会有这样的过程

*   初始化一个空数组
*   遍历所有用户
    *   如果用户是活动的
        *   从头开始推送到数组
            *   但只有用户的名字
*   重复

重构后的方法看起来更像这样

*   获取所有活动用户
*   创建相同大小的新数组
    *   只保存他们的用户名

这就更容易思考和推理了。

* * *

还有许多更有趣的方法。我们再去看看。

## Array.prototype.find

同样，`filter`返回一个包含所有通过测试的项目的数组，`find`返回第一个通过测试的项目。

```
// returns user with id 1
users.find(user => user.id === 1) 
```

Enter fullscreen mode Exit fullscreen mode

> `Array.prototype.findIndex`以同样的方式工作，但是它返回索引而不是项目

对于不需要深度检查的数组，不需要额外函数的开销，你可以简单地分别使用`includes`和`indexOf`。

```
['a', 'b', 'c'].includes('b') //? true
['a', 'b', 'c'].indexOf('a') //? 0
['a', 'b', 'c'].includes('d') //? false
['a', 'b', 'c'].indexOf('d') //? -1 
```

Enter fullscreen mode Exit fullscreen mode

## [T1】array . prototype . some](#arrayprototypesome)

如果至少有一个测试通过，则返回 true。当我们想要检查我们的阵列中是否至少有一个用户是活动的时，我们可以使用它。

使用 for 循环的传统解决方案

```
let activeUserExists = false
for (let i = 0; i < users.length; i++) {
  if (users[i].active) {
    activeUserExists = true
    break
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`some`解决

```
users.some(user => user.active) 
```

Enter fullscreen mode Exit fullscreen mode

## Array .原形.凡

如果所有项目都通过测试，则返回 true。当我们想要检查所有用户是否都是活动的时，我们可以使用这个。

使用 for 循环的传统解决方案

```
let allUsersAreActive = true
for (let i = 0; i < users.length; i++) {
  if (!users[i].active) {
    allUsersAreActive = false
    break
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`every`解决

```
users.every(user => user.active) 
```

Enter fullscreen mode Exit fullscreen mode

## [T1】array . prototype . reduce](#arrayprototypereduce)

如果以上功能都不能帮到你，那就减少 will！它基本上将数组归结为您想要的任何形式。让我们看一个非常简单的数字实现。我们想对数组中的所有数字求和。在传统的 forEach 循环中，它看起来像这样:

```
const numbers = [5, 4, 1]
let sum = 0
numbers.forEach(number => sum += number)
sum //? 10 
```

Enter fullscreen mode Exit fullscreen mode

但是 reduce 函数为我们去掉了一些样板文件。

```
const numbers = [5, 2, 1, 2]
numbers.reduce((result, number) => result + number, 0) //? 10 
```

Enter fullscreen mode Exit fullscreen mode

`reduce`接受两个参数，一个函数和起始值。在我们的例子中，起始值为零。如果我们传递 2 而不是 0，最终结果将是 12。

所以在下面的例子中

```
const numbers = [1, 2, 3]
numbers.reduce((result, number) => {
    console.log(result, number)
    return result + number
}, 0) 
```

Enter fullscreen mode Exit fullscreen mode

日志会显示:

*   0, 1
*   1, 2
*   3, 3

最终结果是最后两个数 3 和 3 的和，所以是 6。

当然，我们也可以把我们的对象数组简化成一个散列表。

按`group`键分组，得到的散列表应该看起来像这样

```
const users = {
  1: [
    { id: 1, name: 'Michael' },
  ],
  2: [
    { id: 2, name: 'Lukas' },
  ],
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用下面的代码
来实现

```
users.reduce((result, user) => {
  const { group, ...userData } = user
  result[group] = result[group] || []
  result[group].push(userData)

  return result
}, {}) 
```

Enter fullscreen mode Exit fullscreen mode

*   `const { group, ...userData } = user`从用户处获取`group`键，并将剩余值放入`userData`中。
*   用`result[group] = result[group] || []`我们初始化这个组，以防它还不存在。
*   我们把`userData`推进新的小组
*   我们为下一次迭代返回新的结果

* * *

## 将这些知识运用在其他可重复项和类似数组的对象上

你还记得以前的这个吗？

**for loop:作用于类似数组的对象**

```
for (let i = 0; i < users.length; i++) {
    //
} 
```

Enter fullscreen mode Exit fullscreen mode

**Array.prototype.forEach:数组原型上的方法**

```
users.forEach(function(user) {
    //
} 
```

Enter fullscreen mode Exit fullscreen mode

**ES6 for of Loop:作用于 iterables**

```
for (const user of users) {
    //
} 
```

Enter fullscreen mode Exit fullscreen mode

您意识到了`forEach`和两个`for`循环的语法有多么显著的不同吗？

为什么？因为两个`for`循环不仅仅作用于数组。事实上，他们甚至不知道数组是什么。

我相信你还记得你的 CS 课上的这种代码。

```
const someString = 'Hello World';
for (let i=0; i < someString.length; i++) {
    console.log(someString[i]);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们实际上可以遍历一个字符串，即使它不是一个数组。

这种`for`循环适用于任何“类似数组的对象”，即具有长度属性和索引元素的对象。

`for of`循环可以这样使用

```
const someString = 'Hello World';
for (const char of someString) {
    console.log(char);
} 
```

Enter fullscreen mode Exit fullscreen mode

`for of`循环适用于任何可迭代的对象。

要检查某个东西是否是可迭代的，你可以使用这个相当优雅的行`Symbol.iterator in Object('pretty much any iterable')`。

在处理 DOM 时也是如此。如果您现在打开开发工具并在控制台中执行下面的表达式，您将得到一个漂亮的红色错误。

```
document.querySelectorAll('div').filter(el => el.classList.contains('text-center')) 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是`filter`不存在于可迭代的 DOM 集合中，因为它们不是数组，因此不共享数组原型中的方法。想要证据吗？

```
(document.querySelectorAll('div') instanceof Array) //? false 
```

Enter fullscreen mode Exit fullscreen mode

但它是一个类似对象
的数组

```
> document.querySelectorAll('.contentinfo')

    NodeList [div#license.contentinfo]
        0: div#license.contentinfo
        length: 1
        __proto__: NodeList 
```

Enter fullscreen mode Exit fullscreen mode

并且也是可迭代的

```
Symbol.iterator in Object(document.querySelectorAll('div')) //? true 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想在可迭代的 DOM 集合上使用新训练的数组知识，我们必须首先将它们转换成合适的数组。

做这件事有两种方法。

```
const array = Array.from(document.querySelectorAll('div')) 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
const array = [...document.querySelectorAll('div')] 
```

Enter fullscreen mode Exit fullscreen mode

我个人更喜欢第一种方式，因为它提供了更多的可读性。

## 结论

我们研究了 array 对象上最重要的方法，并研究了 iterables。如果我们回顾我们开始时制定的目标，我认为可以肯定地说，我们至少完成了

*   逐步思考
*   避免临时变量
*   避免条件句

但是我对`reveal intent`并不完全满意。

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

在下一篇文章中，我们将研究数组的子类化，这样我们就可以提供这样的功能。这也将是 Node.js 单元测试的一个很好的切入点，敬请关注。

P.S .如果你是 Laravel 的粉丝，请看看 [Laravel 收藏](https://www.youtube.com/watch?v=crSUWtRYw-M&t=639s)。

* * *

如果这篇文章对你有所帮助，我有更多关于简化编写软件的技巧[在这里](https://michaelzanggl.gumroad.com/l/intent-driven-development)。