# 单点编程的一个用例

> 原文：<https://dev.to/tvthatsme/a-use-case-for-point-free-programming-1jic>

函数式编程有很多让人喜欢的地方。虽然这不是展示函数式编程风格的好处或权衡的地方，但可以肯定地说，这些概念可以为您的代码带来很多有序性、功能和简单性。然而，在所有这些中，也很容易迷失在术语和数学术语的杂草中。在这篇文章中，我的目标是深入 JavaScript 中的函数式编程、currying 和无指针函数的世界，并解释函数式编程提高简单函数可读性的一个用例。

## 动机

本周，我正在开发一个 JavaScript 函数，它获取一个时间戳并将其转换为一个日期对象，以便比较不同的日期。最初的函数是这样的:

```
/**
 * Take a string with the format "22-03-2019 16:17" and create a new date object with it.
 *
 * @param {String} dateString - The string representation of the date.
 * @returns {Date} - The date represented by the string.
 */
function createDateObjectFromString(dateString) {
  // Destructure both the date and time parts
  const [dateStr, timeStr] = dateString.split(' ')

  // Get the individual parts from each section
  const timeParts = timeStr.split(':')
  const dateParts = dateStr.split('-')

  // Get all the parts needed to create the new date object
  const year = parseInt(dateParts[2], 10)
  const month = parseInt(dateParts[1], 10) - 1
  const day = parseInt(dateParts[0], 10)
  const hours = parseInt(timeParts[0], 10)
  const minutes = parseInt(timeParts[1], 10)

  // Return the new date
  return new Date(year, month, day, hours, minutes, 0, 0)
} 
```

## 我们能稍微清理一下吗？

不可怕，但是在数组的部分有 5 个`parseInt`调用，它不是超级可读的，并且有许多任意的数字需要跟踪。有没有办法清理一下？

当试图简化时，我首先想到的事情之一是问自己是否有办法在一个循环中执行`parseInt`操作。但是这有一个问题:你不能像本文中的[所解释的那样，在`array.map`访问数组时直接使用`parseInt`。](https://raddevon.com/articles/cant-use-parseint-map-javascript)

### 接受太多参数的函数

正如上面链接的文章中所解释的，map 的每次迭代为回调函数提供了 [3 个参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map#Syntax)。然而，`parseInt`接受一个，也可以选择两个参数。这给我们留下了一些奇怪的情况，如果不限制为一个参数，`parseInt`将被调用，基数值为 0、1、2、3 等。这就是为什么在第二次迭代中返回 NaN，因为 1 不是`parseInt`的有效基数值。

好了，这就是为什么`parseInt`在映射字符串数组时不能开箱即用。我们能做些什么呢？

### 默契编程的力量

在计算机科学中有一个被称为[隐性编程](https://en.wikipedia.org/wiki/Tacit_programming)(有时被称为“自由点”)的范例，它通常在函数式编程中使用。类似于 UNIX 管道的工作方式，每个函数只接受一个参数，但是可以组合在一起形成更复杂的函数。如果运用得当，这种范式真的可以简化事情。

现在我们知道了为什么我们不能对字符串数组的每个映射值都进行`parseInt`处理，我们认识到也许一个无点方法可以在这里帮助我们。毕竟当调用`array.map`时，传递给回调函数的第一个参数是我们想要发送给`parseInt`的当前值。我们不关心数组的索引或数组本身——只关心当前值。

### 逢迎救援

编程中的 Currying 是创建一个需要多个参数的函数，但在创建时，并不是所有的参数都是已知的。要创建一个定制函数，需要定义一个接受一个参数的函数，然后返回另一个需要额外参数的函数。当您最终获得了计算所需的所有参数时，您可以调用 curried 函数并将所有参数放在函数的词法范围内。这是因为 JavaScript 的[闭包](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36)概念。

```
function getIntWithRadix(radix) {
  return function parseIntWithRadix(stringParam) {
    return parseInt(stringParam, radix)
  }
} 
```

通过将`parseInt`函数包装在一个 curried 函数中，就有可能预定义基数并创建一个我们可以称之为`getBase10Int`的新函数。

```
const getBase10Int = getIntWithRadix(10) 
```

看到我们在那里做了什么吗？我们为`parseInt`创建了一个具有预定义基数的函数，所以现在我们只需要为该函数提供一个参数。一旦我们这样做了，我们就可以向 map 提供这个定制的函数，并且只接收一个参数，即索引处的值，这将导致通过字符串格式的数字数组进行映射的预期行为，然后能够在每个字符串上调用一种类型的`parseInt`函数。

## 把所有的东西放在一起

```
//////// Setup

/**
 * Create a curried function to get an integer from a string with a specified radix.
 *
 * @param {Number} radix - An integer between 2 and 36 that represents the radix of the string.
 * @returns {(stringParam: String) => Number} - The curried function with one argument.
 */
function getIntWithRadix(radix) {
  return function parseIntWithRadix(stringParam) {
    return parseInt(stringParam, radix)
  }
}

// Define a function to get the integer value from a string with a base 10 radix
const getBase10Int = getIntWithRadix(10)

//////// Usage

/**
 * Take a string with the format "22-03-2019 16:17" and create a new date object with it.
 *
 * @param {String} dateString - The string representation of the date.
 * @returns {Date} - The date represented by the string.
 */
function createDateObjectFromString(dateString) {
  // Destructure both the date and time parts from the string
  const [dateStr, timeStr] = dateString.split(' ')

  // Get the individual parts from each section
  const [hours, minutes] = timeStr.split(':').map(getBase10Int)
  const [day, month, year] = dateStr.split('-').map(getBase10Int)

  // Return the new date (with zero-indexed month)
  return new Date(year, month - 1, day, hours, minutes, 0, 0)
} 
```

## 结论

通过创建一个包装`parseInt`的无点函数，我们现在可以映射到`string.split`上，并使用数组析构来获取我们感兴趣的字符串部分。

## 库和资源

在他的书《Functional-Light JavaScript》的章节[管理函数输入](https://github.com/getify/Functional-Light-JS/blob/master/manuscript/ch3.md/#no-points)中，Kyle Simpson 实际上为`parseInt`使用了类似的无指针函数用例。他的解释值得一读，但他的解决方案是用他创造的函数式编程库实现的。这里介绍的解决方案可以在没有外部依赖的情况下实施，对于快速取胜非常有用。如果您在代码库中看到了多种无点函数的用例，那么 Kyle 的库可能值得一试。

这篇文章是有意不使用提供一元功能的流行实用程序库而写的。这让我们看看最简单的原因和方法，而不仅仅是“安装这个库，你就可以开始了”。也就是说，你很有可能已经在应用程序的其他地方使用了 [Lodash](https://lodash.com/) 、 [Ramda](https://ramdajs.com/) 或其他函数库。如果是这样的话，Lodash 有一个[类似的例子](https://github.com/lodash/lodash/wiki/FP-Guide#capped-iteratee-arguments)用于`parseInt`，Ramda 也有一个[一元函数](https://ramdajs.com/docs/#unary)。我相信还有其他函数库提供类似的解决方案。

这篇文章最初发表在[我的个人博客](https://tvernon.tech/blog/point-free-parseint)上，但是我很想在这里得到一些反馈。感谢阅读！