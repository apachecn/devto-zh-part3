# 为 JavaScript (2018 版)提出的 5 个有用的语言特性

> 原文：<https://dev.to/jakedohm_34/5-helpful-language-features-proposed-for-javascript-2018-edition-4l5h>

## 提案

我在本文中提到的特性已经由推动 JavaScript 向前发展的委员会*向 [TC39](https://github.com/tc39) 提出*，但是还不是 JavaScript 所遵循的标准的一部分。

在 JavaScript 中，一个提议要经过 5 个阶段:Strawman、proposal、Draft、Candidate 和 Finished。

**以下所有特征都是第一阶段提案**，这意味着它们符合提案必须具备的准则，并已提交给 TC39。如果你有兴趣了解更多关于提案和流程的信息，请前往[官方 TC39 提案回购](https://github.com/tc39/proposals)。

现在，为提议干杯！

## 最后一项

建议将`lastItem`添加到数组原型中，这将使抓取数组中的最后一项变得非常容易。我喜欢这个特性，因为我认为这是朝着正确方向迈出的一步，解决了 JavaScript 中一些常见的人机工程学问题。

```
const food = ['🌮', '🥑', '🥓'];

/* New syntax */
const bacon = food.lastItem; // 🥓

/* Old syntax */
const bacon = food[food.length - 1]; 
```

## 可选链接

可选链接是一个建议，它简化了对潜在的`null`或`undefined`类型的属性的访问。

我们需要这样做的原因是，如果没有定义`object.thing`，并且你写了`object.thing.test`，JS 将不仅仅返回`null`或`undefined`，它实际上会抛出一个错误:🚩无法读取未定义的属性“test”。

```
const inputElement = document.querySelector('input[name=email]');

/* New Syntax */
const street = user.address?.street; // returns street value, or null
const inputValue = inputElement?.value; // returns input value, or null

/* Old Syntax */
const street = user.address && user.address.street;
const inputValue = inputElement ? inputElement.value : undefined; 
```

工作原理:如果`?`前的项是`null`或`undefined`，则不处理下一项就返回 null，否则处理“链”的下一位。所以在上面的例子中，如果对象`user` *没有*的键`address`，那么它将返回`null`。如果*做了*，它将处理`.street`部分，并返回`user.address.street`的值。

当我第一次看到这个提议时，我并不支持，但我已经接受了。语法有点古怪，但它肯定会简化你的代码库。

## 空(ish)合并运算符

这个提议允许您检查一个变量或语句是`undefined`还是`null`，如果是，则返回一个回退值。

```
/* New Syntax */
const one = undefined ?? 'Fallback'; // 'Fallback'
const two = null ?? 'Fallback'; // 'Fallback'

/* Old Syntax */
const one = undefined || 'Fallback';
const two = null || 'Fallback'; 
```

从上面的例子来看，你可能认为零合并操作符是完全不必要的，因为它和 or 操作符做同样的事情。但是他们的行为不同。

```
/* The differences between ?? and ||  */

const diffOne = 0 ?? 'Fallback'; // 0
const diffOne = 0 || 'Fallback'; // 'Fallback'

const diffTwo = '' ?? 'Fallback'; // ''
const diffTwo = '' || 'Fallback'; // 'Fallback'

const diffThree = false ?? 'Fallback'; // false
const diffThree = false || 'Fallback'; // 'Fallback' 
```

工作原理:空合并操作符返回第一个不是`null`或`undefined`的东西。

这不同于我们通常使用`||`检查变量的方式，因为 OR 操作符返回第一个真实的东西。这意味着如果值为`false`、空字符串或者`null`，则不会返回；如果其中一个(false、null、空字符串)是有效值，这可能会是个问题。

## 做屏蔽

“do block”是一个非常有趣的提议，但它需要一些时间来适应。语法很简单:你写`do`，然后在括号中输入你想执行的代码。这个特性可以让你做一些在过去很难(或者看起来很糟糕)的事情。比如当你有复杂的标准来定义一个变量:

```
/* New Syntax */
const x = do {
    if (foo()) { f() }
    else if (bar()) { g() }
    else { h() }
};

/* Old Syntax */
let x = foo() ? f() : bar() ? g() : h();

// OR 

let x = (() => {
    if (foo()) return f();
    else if (bar()) return g();
    else return h();
}(); 
```

正如在官方提案中提到的，这种语法在像 JSX 这样的模板语言中特别有用。

```
return (
  <nav>
    <Home />
    {
      do {
        if (loggedIn) {
          <LogoutButton />
        } else {
          <LoginButton />
        }
      }
    }
  </nav>
); 
```

## 全部替换

等等，为什么我们又需要这个？我们已经可以做`string.replace('this', 'that')`了，对吧？但是，`replace`只替换了一个字符串的第一个实例，除非你使用了正则表达式。同样，这个特性解决了一个常见的“小问题”,使事情变得更容易，尤其是对于初学者😄。

```
const string = '248.434.5508';

/* New syntax */
string.replaceAll('.', '-'); // returns '248-434-5508'

/* Old syntax */
string.replace(/\./g, "-"); 
```

## 学习更多

如果你有兴趣了解更多关于这些潜在特性的信息，你可以查看下面官方提议的链接，或者你可以查看所有 JS 提议的。

*   最后一项:[https://github.com/keithamus/proposal-array-last](https://github.com/keithamus/proposal-array-last)
*   可选链接:[https://github.com/tc39/proposal-optional-chaining](https://github.com/tc39/proposal-optional-chaining)
*   零(稍)聚结算子:[https://github.com/tc39/proposal-nullish-coalescing](https://github.com/tc39/proposal-nullish-coalescing)
*   做拦网:[https://github.com/tc39/proposal-do-expressions](https://github.com/tc39/proposal-do-expressions)
*   全部替换:[https://github.com/tc39/proposal-string-replace-all](https://github.com/tc39/proposal-string-replace-all)