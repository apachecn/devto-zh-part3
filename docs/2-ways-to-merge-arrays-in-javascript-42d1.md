# JavaScript 中合并数组的两种方法

> 原文：<https://dev.to/samanthaming/2-ways-to-merge-arrays-in-javascript-42d1>

[![Code Tidbit by SamanthaMing.com](img/cf426b91ae5c238c3997279725961341.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0skPBBtw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r96nzfhwc13koyaexs8p.png)

这里有两种方法来组合你的数组并返回一个新的数组。我喜欢使用扩展运算符。但是如果你需要老版本的浏览器支持，你应该使用 Concat。

```
// 2 Ways to Merge Arrays 

const cars = ['🚗', '🚙'];
const trucks = ['🚚', '🚛'];

// Method 1: Concat 
const combined1 = [].concat(cars, trucks);

// Method 2: Spread
const combined2 = [...cars, ...trucks];

// Result
// [ '🚗', '🚙', '🚚', '🚛' ] 
```

## 替代 Concat 语法

或者，您也可以编写`concat`方法，在这方面:

```
const cars = ['🚗', '🚙'];
const trucks = ['🚚', '🚛'];

const combined = cars.concat(trucks);
// [ '🚗', '🚙', '🚚', '🚛' ]

console.log(cars); // ['🚗', '🚙'];
console.log(trucks); // ['🚚', '🚛']; 
```

如您所见，这种编写方式不会操作或更改现有数组。

我应该选哪一个？

让我们把两个版本都列出来，这样你就可以对比着看了。

```
// Version A:
const combinedA = [].concat(cars, trucks);

// Version B:
const combinedB = cars.concat(trucks); 
```

所以现在的问题是，我应该选择哪一个🤔。我更喜欢版本 A 的**，因为我认为意图更加清晰。只要看着它，我就知道我在创建一个新的数组，而不是操纵现有的数组。然而，如果我看一下**版本 B** ，看起来好像我把`trucks`数组添加到了`cars`数组中，而且对我来说，`cars`数组没有被改变似乎并不明显。但是，也许这只是我。我很想知道你怎么想的？**

因为除了审美之外，我没有什么实质性的理由，所以我认为你和你的团队应该坚持你的选择👍

## Spread 与 Concat 的区别

我更喜欢用`spread`，因为我觉得它更简洁，更容易写。但是，使用`concat`还是有好处的。

当你事先知道你在处理数组时，分布是很奇妙的。但是如果源是其他东西，比如字符串，会发生什么呢？你想把这个字符串添加到数组中。让我们看一个例子。

### 举例:处理任意论点

假设这是我们想要的结果:

```
[1,2,3,'random'] 
```

**A .使用价差**

如果我们遵循我们一直使用的模式，使用 spread 运算符。下面是发生的情况:

```
function combineArray(array1, array2) {
  return [...array1, ...array2];
}

const isArray = [1,2,3];
const notArray = 'random';

combineArray(isArray, notArray);
// 😱 [ 1, 2, 3, 'r', 'a', 'n', 'd', 'o', 'm' ] 
```

☝️:如果我们展开绳子，它会把单词分成单独的字母。所以没有达到我们想要的结果。

**B .使用 Concat**

但是，如果我们按照我们一直在做的连接模式。下面是发生的情况:

```
function combineArray(array1, array2) {
  return [].concat(array1, array2);
}

const isArray = [1,2,3];
const notArray = 'random';

combineArray(isArray, notArray);
// ✅  [ 1, 2, 3, 'random' ] 
```

☝️太棒了！我们得到了我们想要的结果。

我知道你们中的一些人喜欢，咄！我会写一些条件来确保我传递的是一个数组，并相应地执行。当然那也可以。或者只需编写更少的代码并使用`concat`就可以了😂

**判决**

所以这里有一个快速规则。如果你知道你在处理数组，使用`spread`。但是如果你可能要处理非数组的可能性，那么使用`concat`来合并一个数组👍

不管怎样，我只是想指出这一点，这样你就可以根据你要解决的问题使用最合适的方法👍

## 用推送合并数组🤔

你们有些人会问，嘿，我不能也用`push`来合并一个数组吗？是的，你当然可以！但是当你使用`push`的时候，它操纵或者改变现有的数组。它不会创建新的数组。所以取决于你想做什么？一定要记住这一点。

```
const cars = ['🚗', '🚙'];
const trucks = ['🚚', '🚛'];

const combined = cars.push(...trucks);

console.log(combined); // 4
// ☝when you use push, it returns the LENGTH of the combined array

console.log(cars); // [ '🚗', '🚙', '🚚', '🚛' ]
console.log(trucks); // ['🚚', '🚛'] 
```

同样，当你试图把一个数组推到另一个数组时。你将需要传播它，否则，你将得到一个嵌套的数组。当然，除非那是你想要的😜

```
const cars = ['🚗', '🚙'];
const trucks = ['🚚', '🚛'];

cars.push(trucks);
// 😱 cars: [ '🚗', '🚙', [ '🚚', '🚛' ] ]

cars.push(...trucks);
// ✅ cars: [ '🚗', '🚙', '🚚', '🚛' ] 
```

## 浏览器支持

Spread 是在 ES6 中引入的，所以所有现代浏览器都支持它。除了“我太酷了”的网络浏览器，没有其他支持😕。所以如果你需要 IE 支持，你想用`concat`代替或者用类似 [Babel](https://babeljs.io/) 的编译器。

*   [浏览器支持:传播](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#Browser_compatibility)
*   [浏览器支持:串联](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat#Browser_compatibility)

## 资源

*   [MDN Web Docs - Concat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)
*   [MDN 网络文档-传播](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
*   [堆栈溢出:扩展运算符 vs array.concat](https://stackoverflow.com/questions/48865710/spread-operator-vs-array-concat)

* * *

**感谢阅读❤**
问好！[insta gram](https://www.instagram.com/samanthaming/)|[Twitter](https://twitter.com/samantha_ming)|[脸书](https://www.facebook.com/hisamanthaming) | [媒体](https://medium.com/@samanthaming) | [博客](https://www.samanthaming.com/blog)