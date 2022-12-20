# 请不要“超链”数组方法

> 原文：<https://dev.to/somedood/please-dont-overchain-array-methods-49fa>

在现代 JavaScript 出现之前，唯一可能的数组迭代方式是使用经典的 C 风格的`for`循环。至少可以说这很麻烦。它太冗长，有很多样板代码。随着函数式编程概念的日益流行，出现了我们今天喜欢的数组方法。多亏了 [`forEach`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 、 [`map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 、 [`filter`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 和 [`reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight) ，数组的迭代变得前所未有的简单。再加上 [ES6 箭头功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，前所未有的简洁。

除了简洁之外，这些数组方法——本质上只是美化了的`for`循环——还允许我们根据需要链接各种数组操作，而不会牺牲可读性(取决于您的代码)。看到一系列漂亮的连续方法调用真是一件艺术品。看到每个方法调用如何一步一步地操作数组，阅读起来会更加自然。过去需要几行代码才能完成的事情现在只用一行代码就能完成。

尽管数组方法实际上已经消除了对`for`循环的需要，但是它们给表带来了新的问题。尽管一连串的方法调用可能很优雅，但我们必须记住，我们附加到这个链上的每个方法都是一个全新的数组迭代。要编写高性能代码，我们必须记住，这些长链意味着更多的迭代。

# 结合你的数学运算

为了说明不必要的长链问题，考虑从`-2`到`2`的一组数字。我们的目标是求这些数的平方和的三倍。乍一看，我们可以通过一连串的`map`和`reduce`调用来解决这个问题。

```
const nums = [ -2, -1, 0, 1, 2 ];

const sum = nums
  .map(x => x * x)
  .map(x => x * 3)
  .reduce((prev, curr) => prev + curr, 0); 
```

Enter fullscreen mode Exit fullscreen mode

这确实符合我们的目标。唯一的问题是它有三个链接的方法。三个链接的方法意味着三次全新的数组迭代。我们可以通过在返回每个回调函数~~之前添加一个中介`console.log`来证明这个事实，但是我不会在本文中这样做，因为你现在可能已经明白了这一点~~。如果这听起来非常浪费时间，尤其是在大规模的情况下，那么你是对的。为了提高代码的性能，我们只需找到一种方法来组合方法调用，使 CPU 对相同数据数组的迭代次数最少。

```
const nums = [ -2, -1, 0, 1, 2 ];

// Level 1: Combine the `map` calls
const level1Sum = nums
  .map(x => 3 * x ** 2)
  .reduce((prev, curr) => prev + curr, 0);

// Level 2: Combine _everything_
const level2Sum = nums
  .reduce((prev, curr) => prev + 3 * curr ** 2, 0); 
```

Enter fullscreen mode Exit fullscreen mode

# 使用复合布尔表达式

同样的规则也适用于布尔表达式和`filter`方法。假设我们有一个`User`对象的数组。我们想找到目前拥有高级账户的`User`对象。然后，从这些帐户中，我们寻找年龄超过`18`的管理员。

```
class User {
  constructor(isAdmin, hasPremium, age) {
    this.isAdmin = isAdmin;
    this.hasPremium = hasPremium;
    this.age = age;
  }
}

// Array of `User` accounts
const users = [
  new User(false, false, 9),
  new User(false, true, 30),
  new User(true, true, 15),
  new User(true, true, 19),
  new User(false, true, 3)
]; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用复合布尔表达式来组合每个条件，而不是组合数学运算。这样，我们可以最小化数组迭代的次数。

```
// Level 0: Chain _everything_
const level0 = users
  .filter(user => user.isAdmin)
  .filter(user => user.hasPremium)
  .filter(user => user.age > 18);

// Level 2: Combine _everything_
const level3 = users
  .filter(user => (
    user.isAdmin
    && user.hasPremium
    && user.age > 18
  )); 
```

Enter fullscreen mode Exit fullscreen mode

# 利用操作数的省略

同样值得注意的是，仍然可以进一步优化类似的代码。通过巧妙安排布尔条件，代码可以运行得稍微快一点。这是因为 [ECMAScript 规范](https://www.ecma-international.org/ecma-262/9.0/index.html#sec-binary-logical-operators-runtime-semantics-evaluation)规定，一旦遇到计算结果为`false`的表达式，逻辑 AND 运算符(`&&`)必须立即停止计算后续操作数。

```
function willRun() {
  console.log('I just stopped the `&&` operator from evaluating the next operand.');
  return false;
}
function neverRuns() { console.log('This function will never run.'); }

// 'I just stopped the `&&` operator from evaluating the next operand.'
true && willRun() && neverRuns(); 
```

Enter fullscreen mode Exit fullscreen mode

为了编写(稍微)更高性能的代码，更有可能被求值为`false`的布尔表达式必须放在复合布尔条件的开头，以防止后续操作数的不必要的执行和求值。

```
// Arranging conditions properly will
// make your code run slightly faster.
arr.filter(x => (
  x.mostLikelyToBeFalse
  && x.moreLikelyToBeFalse
  && x.likelyToBeFalse
  && x.leastLikelyToBeFalse
)); 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

当然，我举的例子都是琐碎的。运行这些示例不会带来巨大的性能差异，如果有的话。不必要的长迭代链对性能的影响只有在计算成本更高的大规模计算中才会变得明显。对于大多数情况，我们不需要担心。此外，大多数链甚至不超过四的长度。

这篇文章的重点是提醒所有人，仅仅因为我们可以链接方法调用，并不意味着我们应该做得太多。作为开发者，我们有责任确保不滥用这种权力。无论多么微不足道，我们附加到链上的每个方法都会对性能产生影响。如果你应该从这篇文章中学到什么的话，那就是**更长的链意味着更多的迭代**。

*除非你想面对不必要迭代的愤怒，否则请不要“超链”数组方法。*