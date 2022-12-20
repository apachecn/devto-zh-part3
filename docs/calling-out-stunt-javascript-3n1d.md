# 喊出“特技 JavaScript™️”

> 原文：<https://dev.to/lexjacobs/calling-out-stunt-javascript-3n1d>

> 我们不仅要剖析下面这个充满 es6+语法的函数，还要展示一个简单的 es5 实现不仅更容易阅读，而且更高效。

今天，一名学生参加了一个导师会议，想要剖析以下功能:

```
function omit(obj, keyToOmit) {
  return Object.entries(obj).reduce(
    (newObj, [ key, value ]) => (key === keyToOmit ? newObj : { ...newObj, [key]: value }),
    {}
  );
} 
```

你可以说它的功能类似于对象的`filter`。预期的用法是以`obj`的形式传入一个对象，以`keyToOmit`的形式传入一个字符串，然后返回一个新的对象，除了省略的键/值对。

像这样:

```
const dog = { plays: true, guards: true, barksAllNight: true };
let dogUpgrade = omit(dog, 'barksAllNight');
// ahh, now we can get some sleep.
// dogUpgrade => { plays: true, guards: true } 
```

为了理解它是如何工作的，我们把它重新编写成一个不那么显眼，但是更加清晰的实现，从对象中过滤出一个键/值对。

你可能想知道为什么我们不做一些类似
`delete dog[keyToOmit]`的事情；
好问题！这是因为我们不想“改变”最初传递给函数的对象。类似于最初的`omit`函数，我们想要返回一个新的对象，它包含所有最初的键/值对，但不是我们想要过滤掉的键/值对。

### 简单版

这是我们想出的重写版本。如果你在理解原始函数时有任何困难，看看这个函数的可读性如何:

```
function omit(obj, keyToOmit) {
  var result = {}; // start with an empty object (don't mutate the original obj)
  // iterate and only add key/val pairs that are not the keyToOmit
  for (let key in obj) {
    if (key !== keyToOmit) {
      result[key] = obj[key];
    }
  }
  return result;
} 
```

使用一个`for in`循环来遍历对象的键，我们简单地比较`key`和`keyToOmit`，如果不匹配，在最后返回之前扩展我们的`result`对象。

所以现在我们逆向工程了这个函数，并且做了一个基本的实现，我们开始解码原来的`omit`函数中的语法糖。

### 特技版

`Object.entries()`是一个很棒的方法，它返回由对象的键/值对组成的嵌套数组，作为参数传递给方法。

在我们的`dog`上调用`Object.entries`将计算为

```
var entries = Object.entries(dog);
// entries => [["plays", true], ["guards", true], ["barksAllNight", true]] 
```

既然键/值对在一个数组中被序列化了，我们可以使用 HOF(高阶函数)`reduce`将值浓缩成一个最终结果。

`reduce`中回调的函数签名是`(accumulator, currentValue, currentIndex, array)`。最后两个是可选的，在这个函数中没有使用，所以我们现在忽略它们，但是知道它们的存在是很好的。你可以在这里阅读更多关于 mdn 上 reduce 的[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

那么，既然我们**正在**传递前两个参数给`reduce`，那么它们在`omit`函数的上下文中是什么呢？

```
[["plays", true], ["guards", true], ["barksAllNight", true]].reduce(
    (newObj, [ key, value ]) => (key === keyToOmit ? newObj : { ...newObj, [key]: value }),
    {}
  ); 
```

第一个参数`newObj`对应于累加器。第二个写成`[ key, value ]`。这是一个很好的使用*数组析构赋值*的方法，它允许一次对两个变量进行简洁的赋值，对应于传递给 reduce 回调函数的当前值的数组索引。

这里有一个例子:

```
var [a, b] = ['first', 'second'];
// a => 'first'
// b => 'second' 
```

因此，由于 reduce 回调函数在每次迭代中都接收另一个包含关于 dog 的 2 个值(key / value)的数组，所以我们可以使用析构一次给变量`key`和`value`赋值。很好。

现在，在`=>`表达式中，我们可以参考`newObj` `key`和`value`。三元运算符的设置使得如果`key`正好等于`keyToOmit`，我们就返回累加器不变。否则，我们使用对象扩展操作符从累加器中简洁地复制键/值对，并用新的键/值对修改它。`[key]: value`语法是另一个 es6+技巧。它允许为键使用一个*计算属性名*。它计算括号之间的表达式，即`key`变量。计算结果为的字符串成为添加到累加器中的计算键。这个键/值对的值就是`value`变量的值。

在 reduce 函数完成后，整个表达式计算出现在缺少`keyToOmit`的新对象，并从该函数返回。

### 这是超级聪明的编码，但如果大规模使用，效率相当低。

我们来看看吧！

我们低级的 es5 实现不太可能让我们被贴上`1337`的标签，但是让我们考虑它的**运行时复杂性**。

*   进入功能
*   设置结果对象
*   遍历对象的*可枚举属性*一次
    *   将每个值与`keyToOmit`进行比较
*   返回结果对象

现在让我们来分解“特技 JavaScript™️'版”所需的步骤:

*   进入功能
*   通过以下方式导出`Object.entries`:
    *   遍历*可枚举属性*并将键/值对分配给嵌套数组
*   设置缩减功能
*   再次迭代派生的嵌套数组，并
    *   将每个值与`keyToOmit`进行比较
        *   如果匹配，不变地返回累加器
        *   否则，由于对象扩展操作符，再次迭代**！(我认为这将时间复杂度推得更接近`O(n^2)`**

***   返回 reduce 函数的计算结果。**

 **我认为两者在技术上都是运行时间复杂度，但是如果你展示两者的图形，它们都是线性的，但是第二个版本在每个输入大小的操作方面会更陡峭。而且从*空间复杂度(辅助空间)*来看**肯定**效率低。正如细心的读者所指出的，如果对象传播操作符的实现真正地再次遍历累加器的每个键值对，那么这现在就是一个嵌套的 for 循环。这将是一个效率降级，更接近于`O(n^2)`

### 外卖

对于一个简单的助手函数来说，这个时间差是无关紧要的，但是我仍然认为养成考虑算法复杂性的习惯是好的，这样可以建立对运行时的直觉，为在重要的情况下编写高效的代码做准备。

### 最后的话

作为一名新手工程师的老师/导师，我收到了很多关于哪些信息会让那些刚刚踏上编程之旅的人感到困惑的意见。我喜欢写关于这些主题的文章，与更广泛的社区分享这些知识。

我希望您喜欢这次代码探索🔎。更多精彩即将到来，所以请 **+跟随**，我们将在代码之路上再见！

### 两种功能并行:

[https://codepen.io/lexjacobs/embed/MxzxOg?height=600&default-tab=js&embed-version=2](https://codepen.io/lexjacobs/embed/MxzxOg?height=600&default-tab=js&embed-version=2)

[感谢网络甜甜圈提供的免版税封面图片](https://www.pexels.com/@webdonut)**