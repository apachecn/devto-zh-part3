# 设{ [key]: id = 0，...rest } = obj -析构到极限

> 原文：<https://dev.to/michi/let--key-id--0-rest---obj---destructuring-to-the-limit-deo>

我最近遇到了一个问题，我需要下面这段 JavaScript 代码

```
let { [key]: id, ...rest } = obj 
```

Enter fullscreen mode Exit fullscreen mode

所以在这篇文章中，我想解释这是做什么的，以及它是如何工作的。

> 剧透:由于它的晦涩，我最终没有用这种方式实现它，但它仍然很有趣

## 我怎么会碰到这个问题？

假设我们有下面的数组

```
const users = [ 
    { name: 'Michael', group: 1 },
    { name: 'Lukas', group: 1 },
    { name: 'Travis', group: 2 },
] 
```

Enter fullscreen mode Exit fullscreen mode

我们希望通过关键字`group`对其进行分组，并将其转换成类似于
的散列表

```
{
    '1': [
        { name: 'Michael' },
        { name: 'Lukas' },
    ],
    '2': [
        { name: 'Travis' },
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们是如何从`user`对象中移除组的。

我们可以使用
来实现这一点

```
users.reduce((result, user) => {
  const { group, ...userData } = user
  result[group] = result[group] || []
  result[group].push(userData)

  return result
}, {}) 
```

Enter fullscreen mode Exit fullscreen mode

如果你对`reduce`不熟悉，可以看看我关于[数组方法](https://dev.to/mzanggl/array-methods-and-iterables---stepping-up-your-javascript-game-15bo)的文章。

我的最终目标是使这个函数动态化，现在`group`键是硬编码的，没有计算。但是在我们看这个之前，让我们看看`const { group, ...userData } = user`,因为它正是我想要谈论的表达式，只是不是动态的。

## 解构

我们知道每个用户都有键`group`和`name`，所以在 ES6 中我们可以使用一个叫做析构的特性从一个对象中获取单独的值。

例如

```
const { group } = user 
```

Enter fullscreen mode Exit fullscreen mode

会和写
一样

```
const group = user.group 
```

Enter fullscreen mode Exit fullscreen mode

和

```
const { group, name } = user 
```

Enter fullscreen mode Exit fullscreen mode

将同样与
相同

```
const group = user.group
const name = user.name 
```

Enter fullscreen mode Exit fullscreen mode

### 休息

现在我们的初始行多了一个复杂性:`const { group, ...userData } = user`。

`...userData`获取除了`group`之外的所有键值对，并将它们浅层复制到一个名为`userData`的新常量中。在这种情况下，变量`userData`将是一个只有`name`属性的对象。

不要将休止符参数与扩展混淆。传播可能是相反的。

```
const location = { country: 'Japan', city: 'Tokyo' }

const newLocation = { ...location, zipcode: 123456 } 
```

Enter fullscreen mode Exit fullscreen mode

这将位置对象展开，所以`newLocation`将是一个全新的对象，拥有`location`和`zipcode`的所有属性。

什么时候是`rest`什么时候是`spread`？这完全取决于任务是哪一方的。如果有东西在任务的左边，那就是`rest`，如果有东西在任务的右边，那就是`spread`。

您也可以对函数使用 rest 参数。

```
class BaseArray extends Array {
    constructor(...values) { // rest

        super(...values) // spread

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

抛开这些不谈，让我们来看看动态解决方案。

```
function groupBy(array, key) {
    return array.reduce((result, item) => {
        const { [key]: id, ...rest } = item
        result[id] = result[id] || new []

        result[id].push(rest);

        return result;
    }, {})
} 
```

Enter fullscreen mode Exit fullscreen mode

现在`const { [key]: id, ...rest } = item`到底是什么？

我们已经知道了`...rest`的意思，所以我们现在可以忽略它。在解释`[key]: id`之前，我们先来看一个更简单的例子。

### 分配新的变量名

还记得这个吗？

```
const user = { group: 1 }
const { group } = user
console.log(group) //? 1 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想将`group`的值应用到一个不同的变量名会怎么样？我们可以这样做

```
const user = { group: 1 }
const { group: id } = user
console.log(id) //? 1 
```

Enter fullscreen mode Exit fullscreen mode

这将获取`group`的值，并将其放入变量`id`中。

这实际上非常有用，因为有时键作为变量名是无效的。

```
const foo = { 'fizz-buzz': true }
const { 'fizz-buzz': fizzBuzz } = foo 
```

Enter fullscreen mode Exit fullscreen mode

现在我们如何记住这个语法？其实挺简单的。你只需要再想想任务的另一面。当我们创建对象时，我们有完全相同的语法

```
const id = 1
const user = {
    group: id
} 
```

Enter fullscreen mode Exit fullscreen mode

所以如果对象在赋值的右边，我们给对象`user`一个属性`group`来保存变量`id`。

如果它在赋值语句的左边，它将是相反的。

```
const { group: id } = user 
```

Enter fullscreen mode Exit fullscreen mode

我们获取属性`group`的值，并将其放入变量`id`中。

### 最后，计算对象属性名称

所以剩下唯一要解释的就是`[key]`。

我们可以用它来访问一个计算的属性名，在我们的例子中，变量`key`保存值`group`。

同样，这里没有什么新的东西。

创建对象时如何添加计算键？

使用相同的语法，只是它在赋值的右边！

```
const key = 'group'
const id = 1

const user = {
    [key]: id
} 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们写下`let { [key] } = obj`，我们应该用什么名字来访问这个变量呢？嗯，我们不能，所以像用`fizz-buzz`一样，我们需要用`:`把它赋给一个新变量。这种结合最终创造了`[key]: id`。

那就这样吧，我们怎样才能让它变得更加晦涩呢？通过对 id 应用默认值！

通常它看起来像这样

```
const user = { group: 1 }

const { group = 0, createdAt = null} = user 
```

Enter fullscreen mode Exit fullscreen mode

使用计算的属性，它变成

```
let { [key]: id = 0, ...rest } = obj 
```

Enter fullscreen mode Exit fullscreen mode

参考

*   [解构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)
*   [给新变量名赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Assigning_to_new_variable_names)
*   [计算对象属性名称](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Computed_object_property_names_and_destructuring)