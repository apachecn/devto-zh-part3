# JavaScript 中的可变长度 currying

> 原文：<https://dev.to/charlesstover/variable-length-currying-in-javascript-1g5m>

这是一个有趣的编程挑战，reddit 用户`i7_leaf`声称收到了[一个采访问题](https://www.reddit.com/r/javascript/comments/9vxdkx/was_asked_this_js_interview_question_is_this_even/)。

# 前言⭐

原始问题和我将在本文中涉及的内容之间有一个关键的区别。面试问题要求考生写一个函数，执行如下:

```
addSubtract(1)(2)(3);          // 1 + 2 - 3             = 0
addSubtract(1)(2)(3)(4)(5)(6); // 1 + 2 - 3 + 4 - 5 + 6 = 5 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，这个 curried 函数不以任何类型的定界符结束，例如终止方法`.execute()`或空参数`()`。让这个挑战既困难又有趣的是缺少“这是序列中的最后一个数字”的信号。

我同意讨论帖中的大多数评论，即面试官并不是有意问这个问题*本身*。按照假设，这个函数不可能存在。`addSubtract(1)(2)(3)`不可能既是原语(第一个例子中的数字 0)又是函数(第二个例子中接受 4 作为参数)。

也就是说，这是*概念上的*可能，只需稍微调整一下。虽然下面两个陈述*不可能都*为真，但第三个陈述*可以*。

```
// This cannot be true with the following statement.
addSubtract(1)(2)(3) === 0;

// This cannot be true with the preceding statement.
addSubtract(1)(2)(3)(4)(5)(6) === 5;

// This can be true:
addSubtract(1)(2)(3) + addSubtract(1)(2)(3)(4)(5)(6) === 5;

// These can be true too:
+addSubtract(1)(2)(3) === 0;
+addSubtract(1)(2)(3)(4)(5)(6) === 5; 
```

Enter fullscreen mode Exit fullscreen mode

我相信面试官实际上是在测试应聘者编写一个交替加减运算的可编程函数的能力，但他并没有意识到这两个函数的例子是互斥的。第三个陈述中的场景是 JavaScript 的一个模糊功能，所以我不支持把它用作面试问题。这是一个“跳出框框思考”的解决方案，但并不实用。

# 它是如何工作的？🤔

使用内置方法可以将任何对象类型转换为字符串或数字。

第三个示例中使用的`+`试图将参数的两端类型转换为`Number`类型。如果没有办法将类型强制转换为`Number`类型，它将尝试将类型强制转换为`String`类型(同样的方式`'Hello ' + 123 === 'Hello 123'`)。正是因为我们能够显式定义如何对对象进行类型转换，我们才能够解决`addSubtract(1)(2)(3) + addSubtract(1)(2)(3)(4)(5)(6)`的问题，也正是因为 JavaScript 在单独调用对象时缺乏类型转换，所以它无法知道`addSubtract(1)(2)(3)`是原语`0`。事实上，它不是本原数`0`。它是一个函数，这就是为什么我们既可以把它当作一个可以类型转换的对象，又可以把它当作一个函数来调用:

```
const x = addSubtract(1)(2)(3); // function
+x;    // type cast to 0
+x(4); // type cast to 4 
```

Enter fullscreen mode Exit fullscreen mode

当把一个对象(或函数)当作一个数字时，会调用该对象的`valueOf`方法，其返回值就是用于数值运算的内容。当把一个对象当作一个字符串时，会调用那个对象的`toString`方法。

```
const myStrObject = {
  toString: function() {
    return 'Str';
  }
};
console.log('My object is ' + myStrObject); // 'My object is Str'
console.log(myStrObject + 297);             // 'Str297'

const myNumObject = {
  valueOf: function() {
    return 123;
  }
};
console.log('My object is ' + myNumObject); // 'My object is 123'
console.log(myNumObject + 297);             // 420 
```

Enter fullscreen mode Exit fullscreen mode

# 让我们咖喱🍛

这就是解决这个问题所需的全部介绍，所以我将提供解决方案。

```
// Given an array of numbers, if the index is even, add.
//   If the index is odd, subtract.
const addSubtractReducer = (total, current, index) =>
  (index % 2) === 0 ?
    total + current :
    total - current;

const addSubtract = x => {
  const nums = [ ];

  // Recursive function that accumulates numbers for the operation.
  const f = y => {
    nums.push(y);
    return f;
  };

  // When the recursive function is type cast to a number,
  //   reduce the accumulated numbers.
  f.valueOf = () => {
    return nums.reduce(addSubtractReducer, x);
  };

  // Return the recursive function, having added the first digit.
  return f;
}; 
```

Enter fullscreen mode Exit fullscreen mode

为了可读性，我在 main 函数之外定义了 reducer 函数。为了更好地封装，您可能更喜欢将它包含在函数中。

减法器只是交替加减运算。给定一个运行总数和一个新数字，如果它是一个偶数指数，加；如果是奇数指数，就减去。

递归函数`f`用于查询参数。每次对`f`的函数调用都会返回`f`，这让你可以令人厌烦地调用它，每次都将新的参数添加到我们将要加减的数字数组中。

函数`f`有一个`valueOf`属性。当我们将`f`类型转换为一个数字时，这个属性将被调用。从第一个提供的数字(`x`)开始，这个`valueOf`属性使用前面提到的交替操作减少剩余的数字。

对`addSubtract`的第一次调用在创建递归函数`f`后返回它。

# 局限性🙅

在典型的面试中，这个解决方案并不完美。如果面试官问你一些限制，那么在缓存任何函数调用的返回值时会有奇怪的行为。第一次调用`addSubtract`之后的每个函数调用都将使用相同的`nums`数组。这可能会导致意外行为。

```
const addTo1 = addSub(1); // nums = [1]
+addTo1(2); // 3             nums = [ 1, 2 ]
+addTo1(2); // 1             nums = [ 1, 2, 2 ] 
```

Enter fullscreen mode Exit fullscreen mode

`nums`数组存储在`addTo1`中。第一次调用将值加 2。第二次调用从值中减去 2。这可以通过为每个函数调用返回一个新的数组实例来解决。最初的面试问题没有规定这是一项要求，所以我选择提供代码复杂度较低的解决方案。

# 结论🔚

我喜欢这个问题的晦涩，用户似乎也喜欢我的解决方案。出于对 JavaScript 语言的热爱，我决定分享一下。如果你是面试官，不要在面试时问这个问题。确保你的例子有相同数量的参数或终止方法。作为一名 JavaScript 专家，我不认为这是一个适合 JavaScript 理解的面试问题。这些知识有助于解决难题，但对更好的开发人员来说却不是。如果你是被面试者，不要期望在面试中出现这种情况。请面试官澄清不同的参数长度。这可能是他们的一个错误。(如果不是，至少现在你知道怎么解决了！)

如果你喜欢这篇文章，请随意给它一颗心或一只独角兽。又快又简单，还免费！如果您有任何问题或相关见解，请留下评论。

要阅读我的更多专栏或联系我，你可以在 [LinkedIn](https://www.linkedin.com/in/charles-stover) 、 [Medium](https://medium.com/@Charles_Stover) 和 [Twitter](https://twitter.com/CharlesStover) 上找到我，或者在 CharlesStover.com 上查看我的作品集。