# 递归、记忆和 Y 组合子

> 原文：<https://dev.to/myterminal/recursion-memoization-and-y-combinator-174l>

[![](img/7d024c902722b00e0209749173df200e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--avNYu_Rv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dc7rjzpyeuiqbyse1es3.png)

在这篇文章中，我们将从不同的角度来看递归，尝试在不容易实现的环境中实现它，讨论记忆化，看看如何记忆一个递归函数。

我们将把**乐趣**动作传递给**乐趣**动作，产生新的**乐趣**动作，这样我们就可以有更多**乐趣**动作的**乐趣**动作。

## 本文性质

这篇文章有一点理论和许多代码片段。该理论将提供丰富的信息，代码实验将非常有趣。

我们将在文章中使用 JavaScript。

## 本文选择 JavaScript 的原因

*   它是干净的
*   它是一种功能性编程语言
*   它是大多数开发人员熟悉的语言之一
*   太棒了。

## 先说一些理论

在开始真正的内容之前，让我们快速浏览一下一些编程术语，其中大部分将有助于更好地理解本文的其余部分。

本文将主要使用 JavaScript 中的新(ES2015+)结构，但为了简单和比较起见，有时会使用旧语法。

## 理论(1/8):JavaScript 函数式编程

毫无疑问，JavaScript 和函数式编程配合得很好。

美国计算机程序员和 JavaScript 专家道格拉斯·克洛克福特称 JavaScript 是披着 T4 外衣的 T2 LisP T3。

考虑一个不寻常的函数，比如:

```
const apply = function (func, value) {
    return func(value);
}; 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
const apply = (func, value) => func(value); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，函数`apply`接受一个函数作为第一个参数，就像我们接受一个值作为第二个参数，它可以是数字、字符串、布尔值等形式。

这两个参数之间没有明显的区别，直到您仔细观察第一个参数被调用，第二个参数作为参数传递给它。

## 理论(2/8):高阶函数

*高阶函数*只不过是接受其他函数作为输入和/或返回一个函数作为输出的函数，而不是传统函数中的值。如果一个函数至少具有这两个特征中的一个，就可以称它为高阶函数。

例如，考虑这个函数:

```
const isDivisibleBy = function (n) {
    return function (x) {
        return x % n === 0;
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
const isDivisibleBy = n => x => x % n === 0; 
```

Enter fullscreen mode Exit fullscreen mode

它可以用作:

```
const isDivisibleByTwo = isDivisibleBy(2);

isDivisibleByTwo(3)    // Returns false
isDivisibleByTwo(6)    // Returns true 
```

Enter fullscreen mode Exit fullscreen mode

函数`isDivisibleBy`是一个高阶函数，当提供数字输入时，它提供一个函数来检查提供的数字是否能被创建函数时使用的数字整除。

## 理论(3/8):同一性函数

根据定义，*恒等函数*是一个返回与其参数相同的值的函数。尽管它看起来没什么用，但有时却非常有用。

只是为了让它听起来更有用一点(如果你到现在还没有猜到)，我们将在本文中使用它。

以下是几种不同编程语言中标识函数的一些表示形式:

```
// JavaScript (ES2015+)
const identity = input => input;

// C#
Func<int, int> identity = x => x

// LisP
(defun identity (x)
    x)

// Haskell
identity x = x

// Lambda Calculus
位 x.x 
```

Enter fullscreen mode Exit fullscreen mode

简而言之:“你得到你所给予的”。

当你必须提供一个函数而不是值时，它可能是必需的，但是它不一定有一个行为。

## 理论(4/8):自由变量

函数中的*自由变量*是一个既不是自身局部的变量，也不是自变量之一。它可以从函数内部访问，但属于函数外部的作用域。

考虑下面的函数:

```
const addToSomething = function (number) {
    return someValue + number;
}; 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
const addToSomething = number => someValue + number; 
```

Enter fullscreen mode Exit fullscreen mode

为了让这两个函数定义都能工作，您需要一个名为`someValue`的变量，它在函数内部是可访问的。

```
const someValue = 256; 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经猜到了，`someValue`是一个自由变量。

这里的代码可能工作正常，但是对于编程纯粹主义者来说，这是一个坏习惯。

## 理论(5/8):参照透明

如果用它的值替换一个表达式，它不会改变程序的行为，那么这个表达式可以被称为*引用透明的*。

以下面的函数为例

```
const square = a => a * a; 
```

Enter fullscreen mode Exit fullscreen mode

和下面的表达式

```
square(square(square(square(2)))); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用各自的值替换`square(arg)`的实例，表达式在每一步都变得越来越简单。

```
square(square(square(4))); // Step 1

square(square(16));        // Step 2

square(256);               // Step 3

65536                      // Step 4 
```

Enter fullscreen mode Exit fullscreen mode

考虑下面这个函数**不是透明的**(可能是透明的？):

```
let count = 2;

incrementCount = step => {
    count = count + step;
    return count;
}; 
```

Enter fullscreen mode Exit fullscreen mode

它不能被它的值替换，因为这样做会改变结果表达式的值。

有一件事绝对阻碍了这个函数的引用透明性:它产生了一个副作用。这意味着在它执行的时候，它改变了一个它不拥有也不应该拥有的变量的值。

另一件事是(你现在应该已经猜到了)，它有一个自由变量。

**结论:**对于一个引用透明的函数，它不应该有任何自由变量。

## 理论(6/8):坦能对应原理

这个原理说明，如果一个表达式被一个 lambda(想象一下 JavaScript 中的一个函数)包围，并立即调用它，那么最终的表达式返回的结果与原始表达式相同。

例如，如果我们替换这个表达式

```
1 + 2 
```

Enter fullscreen mode Exit fullscreen mode

用一个函数包装它并立即调用它

```
(() => 1 + 2)() 
```

Enter fullscreen mode Exit fullscreen mode

结果表达式返回值。

## 理论(7/8):行内定义

*函数的内联定义*是指程序中的任何函数都可以被它的函数定义代替。

最终的程序将返回相同的结果。

例如，如果我们有一个名为`add`的函数定义，如下所示，

```
const add = (a, b) => a + b; 
```

Enter fullscreen mode Exit fullscreen mode

然后

```
add(1, 2) 
```

Enter fullscreen mode Exit fullscreen mode

应该与相同

```
((a, b) => a + b)(1, 2) 
```

Enter fullscreen mode Exit fullscreen mode

## 理论(8/8):λ演算

维基百科上的一个非常专业的定义说

> λ演算(也写作位-calculus)是数理逻辑中的正式系统，用于基于使用变量绑定和替换的函数抽象和应用来表达计算。这是一个通用的计算模型，可以用来模拟任何图灵机。它是由数学家阿隆佐·邱奇在 20 世纪 30 年代首次提出的，作为他研究数学基础的一部分。

更简单(可能不太准确)的定义是使用匿名函数。

在[维基百科](https://en.m.wikipedia.org/wiki/Anonymous_function)中提到:

> 在计算机编程中，匿名函数(函数文字、lambda 抽象或 lambda 表达式)是不绑定到标识符的函数定义。匿名函数通常是传递给高阶函数的参数，或者用于构造需要返回函数的高阶函数的结果。[1]如果函数只使用一次，或者使用有限的次数，那么从语法上来说，匿名函数可能比使用命名函数更简单。

## 递归

在计算机程序设计中，*递归*是一种通过将原问题分成相同或相似类型的更小的子问题来解决问题的技术。递归最常见的例子是阶乘函数。

通常，当一个函数知道-

*   如何将问题分解成更小的问题
*   何时停止进一步打破问题
*   如何将结果值返回给调用函数

考虑下面计算正数阶乘的函数。

```
const factorial = num => {
    if (num < 2) {
        return 1;
    } else {
        return num * factorial(num - 1);
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

由于显而易见的原因，函数`factorial`没有被设计成干净的。

至少有一个迹象表明它是不干净的:有多个 return 语句，这是在编写函数时应该避免的，除非真的有必要。

在函数中，假设我们知道如何计算较小数字的阶乘，则可以计算一个数字的阶乘。委托过程继续进行，直到已知当前数的阶乘，并且不再需要委托。

## λ演算中的递归

使用 lambda 表达式时，不可能编写递归函数(至少不能直接编写)。

原因是 lambda 演算处理匿名函数。

匿名函数(显然是没有名字的)没有名字可以引用，就没有办法调用自己，甚至对自己也是如此。

这太可悲了，我们不能到处使用递归。肯定有办法的，对吧？

另外，文章还没到结尾的一半。振作起来！

## 组合子

同时，在编程世界的另一个角落，一个*组合子*是*一个没有自由变量*的函数或定义。

我们现在知道了什么是自由变量，看看上面的定义，我们可以有把握地说，组合子是一个纯 lambda 表达式(根据定义，这意味着，“不访问全局变量或定义在函数范围之外的变量”)。

有各种各样的组合子。

## Y 组合子

魔法来了:

Y 组合子是一种组合子，可以用λ表达式实现递归，这样匿名函数就可以调用自己。

**甜！**

让我们用 JavaScript 导出一个 Y 组合子，并自己验证它是否有效，并提供他们所说的功能。

## 让我们推导 Y-Combinator

有趣的部分来了:**是时候写一些代码了！**

## 我们的助手功能

派生 Y 组合子的活动将涉及多个步骤。

认识我们的助手，帮助我们开展活动:

```
const printResults = function (f) {
    const output = [];

    for (var i = 0; i < 10; i++) {
        output.push(i.toString() + ': ' + f(i).toString());
    }

    return output.join('; ');
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们将从一个简单的函数开始，它“有点”做我们需要的事情，我们运行一些测试，找到缺失的部分，并在下一步中改进它。

经过几次迭代，我们最终会得到我们想要的东西，并且会有蛋糕。

但是对于每一步，我们都需要一个助手函数来测试我们当前的实现是否做了它应该做的事情。

我们可以测试函数的特定迭代是否如预期的那样运行的方法之一是将一定范围的值作为输入传递给函数，并观察为这些值产生的输出。

出于本练习的目的，让我们取 0 到 9 之间的数字，这是一个合理的输入值范围，以便知道我们已经达到了创建我们正在寻找的函数的目标。

洗了几次并涂了几层蜡之后:

```
const printResults = f => {
    const outputs = (new Array(10))
                        .join('.')
                        .split('.')
                        .map(
                            (e, i) => 
                                `${i.toString()}: ${f(i).toString()}`
                        );

    return outputs.join('; ');
}; 
```

Enter fullscreen mode Exit fullscreen mode

该函数基本上做同样的事情，但有一些不同:

*   我们用箭头函数替换了所有的函数关键字
*   不再有`for`循环
*   每次迭代中的字符串连接都被替换为模板字符串

## 阶乘:我们知道

我们大多数人都知道什么是阶乘，并且在一些编程语言中看到过一两个阶乘函数的实现。

我们在之前的幻灯片中也看到了一个。

我们可以用许多形式来写它，下面是一个简化的版本。

```
const factorialRecursive = n =>
    (n < 2)
        ? 1
        : (n * factorialRecursive(n - 1)); 
```

Enter fullscreen mode Exit fullscreen mode

不是很复杂，是吗？

然而，如果我们的语言没有递归的特性，你知道如何实现同样的函数吗？

## 无递归阶乘:一种粗略的尝试

让我们从尝试创建一个没有递归的阶乘函数开始。

我们知道会有很多次迭代，第一次肯定会失败。因此，让我们称之为*迭代 0* 。

```
const recurse_v0 = (n, nextStep) => {
    if (n < 2) {
        return 1;
    } else {
        return n * nextStep(n - 1);
    }
};

const identity = n => n; 
```

Enter fullscreen mode Exit fullscreen mode

由于不允许(或不能)调用自身，函数`recurse_v0`接受一个需要计算阶乘的数字，以及一个描述下一步要做什么的函数。

函数`nextStep`试图模拟*将问题分解成一个更小的问题(递归调用)*。

由于我们不知道下一步会是什么样子，我们现在将使用一个身份函数作为下一步。

因此，对于每个数字，我们(尝试)将阶乘计算为:

```
n => recurse_v0(n, identity); 
```

Enter fullscreen mode Exit fullscreen mode

为了打印(伪)阶乘，我们可以使用我们的助手函数:

```
printResults(n => recurse_v0(n, identity)); 
```

Enter fullscreen mode Exit fullscreen mode

对于 0 到 9 的值，只有当输入等于或小于 3 时，函数才会执行它应该执行的操作。

这只是证明了一些我们已经知道的事情:身份功能对我们的事业没有帮助。我们需要解决这个问题。

## Y 组合器:第一步

让我们从把我们当前拥有的变成一个函数开始，这个函数创建一个函数来创建任何指定数字的阶乘，但是接受一个描述下一步的函数。

```
const recurse_v1 = nextStep =>
    n => {
        if (n < 2) {
            return 1;
        } else {
            return n * nextStep(n - 1);
        }
    }; 
```

Enter fullscreen mode Exit fullscreen mode

在`recurse_v1`中，我们接受一个函数作为下一步，并返回一个函数，该函数被认为是一个阶乘函数，它接受一个数字并返回该数字的阶乘。

为简单起见，下一步我们还是用恒等函数。

在上面运行我们的助手函数，

```
printResults(recurse_v1(identity)); 
```

Enter fullscreen mode Exit fullscreen mode

我们发现 0 到 9 之间的数字的输出与上一次迭代相同，只是我们通过下一步、接受输入和生成阶乘函数的方式发生了变化。

话虽如此，大于 3 的数字的输出仍然不正确。

然而，通过我们所做的更改，我们可以用一些额外的代码找到大于 3 的数字的阶乘。

例如，如果我们需要创建一个可以找到 4 的阶乘的函数，我们可以添加一层嵌套，如下所示:

```
printResults(recurse_v1(recurse_v1(identity))); 
```

Enter fullscreen mode Exit fullscreen mode

这为我们提供了从 0 到 4 的正确答案。

现在我们(差不多)有了一个解决方案，我们可以找到我们的函数应该处理的最大数量，并创建这么多嵌套，酷！

或者，我们可以找到一种更干净、更可靠的方法，而不需要创建一个只对某个范围内的数字有效的函数。

## Y 组合器:第二步

在开始使用更简洁的方法之前，先将计算阶乘的逻辑分离到父函数之外怎么样？

这样，我们就可以创建一个通用函数来实现递归，不仅可以计算阶乘，还可以计算其他递归函数。

通用函数可以提取为

```
const factorialStepper = nextStep =>
    n => {
        if (n < 2) {
            return 1;
        } else {
            return n * nextStep(n - 1);
        }
    }; 
```

Enter fullscreen mode Exit fullscreen mode

现在阶乘的逻辑已经不存在了，我们的下一次迭代可能是这样的

```
const recurse_v2 = stepper =>
    n => stepper(identity)(n); 
```

Enter fullscreen mode Exit fullscreen mode

实现现在独立于我们需要创建的实际递归函数。

为了查看我们的函数现在的行为，让我们用我们的助手函数来测试这个迭代。

```
printResults(recurse_v2(factorialStepper)); 
```

Enter fullscreen mode Exit fullscreen mode

如果你看到，行为是完全一样的。

事实上，像我们在上一次迭代中那样添加更多的嵌套是不可能的了，如果我们试图这样做，它会崩溃。

让我们进一步继续这项活动。

## Y 组合器:第三步

为了能够添加更多的嵌套，我们需要对上一次迭代进行一些修改。

```
const recurse_v3 = stepper => {
    const function1 = 
        n => stepper(identity)(n);

    return n => stepper(function1)(n);
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们需要用类似于输出函数的东西来代替身份函数。

有了这个改变，我们又进了一步，所以现在我们可以找到 4 的阶乘。

```
printResults(recurse_v4(factorialStepper)); 
```

Enter fullscreen mode Exit fullscreen mode

虽然这不是我们所需要的全部，但这是一些进步。

## Y 组合器:第四步

为了增加我们功能的范围，我们可以增加另一个步骤:

```
const recurse_v4 = stepper => {
    const function2 = 
        n => stepper(identity)(n);

    const function1 = 
        n => stepper(function2)(n);

    return n => stepper(function1)(n);
}; 
```

Enter fullscreen mode Exit fullscreen mode

有了这个额外的步骤，我们现在可以计算高达 5 的数字的阶乘。

这就把我们带到了一个我们以前见过的情况。我们可以决定我们希望能够计算阶乘的最大数是多少，并创建一个能够这样做的函数。

然而，正如我们上次看到的，我们需要更有能力的东西。

简而言之，我们需要一个用更小的值不断调用下一步并在不再需要递归时停止的函数。我们已经用步进函数处理了第二部分。我们仍然需要一种机制来根据需要多次调用内部函数。

## Y 组合器:第五步

让我们尝试创建一个函数来替换所有编号的函数，如`function1`、`function2`等。并且能够动态地创建它们。

```
const recurse_v5 = stepper => {
    const makeStep = nextStep =>
        n => stepper(nextStep)(n);

    return n => stepper(makeStep)(n);
}; 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，这应该是可行的，但事实并非如此。

原因是`makeStep`的结构与其要替代的功能不同。早期的函数用来接受一个数字，而这个函数接受下一步，这本身就是一个函数。

还没有，但我们已经很接近了。

## Y 组合器:第六步

为了修复我们在上一步中面临的问题，将`makeStep`传递给`makeStep`似乎可以解决问题。

我们也可以通过将`nextStep`传递给`nextStep`来在`makeStep`函数中做类似的改变，这保持了对称性。

```
const recurse_v6 = stepper => {
    const makeStep = nextStep =>
        n => stepper(nextStep(nextStep))(n);

    return n => stepper(makeStep(makeStep))(n);
}; 
```

Enter fullscreen mode Exit fullscreen mode

将上述函数传递给我们的 helper 函数似乎提供了一个很好的结果:我们有了适用于所有输入的东西！

## Y 组合器:第 7 步

为了简化，我们可以将 *Tennent 对应原理*应用到我们的输出函数中，并将函数改写为:

```
const recurse_v7 = stepper => {
    const makeStep = nextStep =>
        n => stepper(nextStep(nextStep))(n);

    return (func =>
        n => stepper(func(func))(n)
    )(makeStep);
}; 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
const recurse_v7 = stepper => {
    const makeStep = nextStep =>
        n => stepper(nextStep(nextStep))(n);

    return (nextStep =>
        n => stepper(nextStep(nextStep))(n)
    )(makeStep);
}; 
```

Enter fullscreen mode Exit fullscreen mode

你看到一种模式了吗？

## Y 组合器:第 8 步

匹配这里的某些模式，我们可以将整体功能简化为:

```
const recurse_v8 = stepper => {
    const makeStep = nextStep =>
        n => stepper(nextStep(nextStep))(n);

    return makeStep(makeStep);
}; 
```

Enter fullscreen mode Exit fullscreen mode

这就给我们带来了我们需要的东西:我们几乎已经有了 Y-combinator 的实现。

## Y 组合子:我们成功了！

移除所有局部变量并应用*内联定义*，我们得到:

```
const yCombinator = stepper =>
    (
        nextStep => n => stepper(nextStep(nextStep))(n)
    )(
        nextStep => n => stepper(nextStep(nextStep))(n)
    ); 
```

Enter fullscreen mode Exit fullscreen mode

或者你可以把它写成

```
const yCombinator = stepper =>
    (
        f => f(f)
    )(
        nextStep => n => stepper(nextStep(nextStep))(n)
    ); 
```

Enter fullscreen mode Exit fullscreen mode

> 这种方法的成功确实非常了不起。——[苏斯曼](https://en.wikipedia.org/wiki/Gerald_Jay_Sussman)和[斯蒂尔](https://en.wikipedia.org/wiki/Guy_L._Steele_Jr.)

我们现在拥有的是一个*神奇的*函数，它可以用来在一个函数不能引用自身的环境中实现类似阶乘的递归函数。

## 不带递归的阶乘:使用 Y 组合子

现在我们有了 Y 组合子，我们可以很容易地实现以前做不到的事情。

概括一下，我们有专门为阶乘设计的步进函数:

```
const factorialStepper = nextStep =>
    n => (n < 2) ? 1 : (n * nextStep(n -1)); 
```

Enter fullscreen mode Exit fullscreen mode

当我们将我们的组合子应用于它时，

```
const factorial = yCombinator(factorialStepper); 
```

Enter fullscreen mode Exit fullscreen mode

我们得到一个阶乘函数。

我们可以通过使用旧的 helper 函数来检查我们所拥有的。

```
printResults(factorial); 
```

Enter fullscreen mode Exit fullscreen mode

概述:

*   我们从一个函数不可能引用自身并且几乎不可能实现递归函数的情况开始
*   我们尝试了一些不用递归实现阶乘的方法
*   在这个过程中，我们将 factorial 的实现从正在讨论的函数中提取出来，使它更通用，这样它就可以用于实现任何递归函数
*   我们现在证明了新创建的*神奇的*函数可以用来实现阶乘

现在是时候尝试我们的*神奇的*函数来实现阶乘之外的东西了，这样我们就可以证明我们的函数可以处理任何东西。

## 我们所知道的斐波那契

斐波那契是我们在讨论递归时听到的另一个流行术语。

斐波纳契在可能递归的宇宙中的一个可能实现是:

```
const fibonacciRecursive = function (n) {
    return (n < 2) 
        ? 1 
        : (fibonacciRecursive(n - 2) + fibonacciRecursive(n - 1));
}; 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
const fibonacciRecursive = n =>
    (n < 2) ? 1 : (fibonacciRecursive(n - 2) + fibonacciRecursive(n - 1)); 
```

Enter fullscreen mode Exit fullscreen mode

这是另一个很好的例子，为了计算特定输入值的输出，函数依赖于一个事实(或假设),即较小值的输出是已知的。它将问题分解成更小的子问题，并继续这样做，直到达到不再需要分解的程度。

## 无递归的斐波那契:使用 Y-Combinator

仅仅为了让我们的生活再次变得困难，想象一个没有递归可用的环境。

幸运的是，我们有我们的`yCombinator`。

现在我们可以使用一个`fibonacciStepper`并使用我们的组合子来创建一个斐波那契实现，即使是这样的*限制*环境。

```
const fibonacciStepper = nextStep =>
    n => (n < 2) ? 1 : (nextStep(n - 2) + nextStep(n - 1));

const fibonacci = yCombinator(fibonacciStepper); 
```

Enter fullscreen mode Exit fullscreen mode

为了测试我们的函数的行为，我们有一个助手函数:

```
printResults(fibonacci); 
```

Enter fullscreen mode Exit fullscreen mode

看到组合子也可以用来实现 Fibonacci 而不用递归，我们可以得出结论，我们现在有一个 Y 组合子的实现，它可以用于任何步进函数。

## 记忆化

在编程中，记忆化是一种优化技术，它利用以前计算的结果来加速计算。

在一个典型的实现中，维护一个缓存来存储具有相似输入的先前计算的结果，以便我们有可供参考的输出。

```
const memoize = func => {
    const cache = {};

    return input => {
        if (!cache[input]) {
            cache[input] = func(input);
        }

        return cache[input];
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们的`memoize`函数是一个高阶函数，它接受一个函数并返回一个包装器。

返回的函数被记忆，因此当多次提供相同的输入时，一旦知道特定输入值的输出，就不必调用原始函数。输出直接从缓存中获取。

我们在这里看到的*过于简单的*实现不太实用。有更好的方法来实现多输入参数。

为了简单起见，我们已经为单个输入参数设计了一个。

有一些编程语言在语言本身中内置了这样的功能。

对于其他人，我们要么使用库，要么自己写一个。

## 【替身】功能:我们知道

考虑下面的函数，它将提供给它的任何输入加倍:

```
const double = a => a + a; 
```

Enter fullscreen mode Exit fullscreen mode

为了在每次调用函数时都得到通知，我们在函数中放一个 log 语句。

```
const doubleLogged = a => {
    console.log(`Calculating the double of ${a}`);

    return a + a;
}; 
```

Enter fullscreen mode Exit fullscreen mode

考虑一个假设的情况，当我们需要多次调用函数`doubleLogged`时，很可能使用重复的输入值。

```
[1, 2, 3, 1, 5, 1].forEach(n => console.log(`Double of ${n} is ${doubleLogged(n)})); 
```

Enter fullscreen mode Exit fullscreen mode

正如您所料，函数`double`被调用了`6`次，其中，`3`被调用了三次，输入为`1`。

现在，假设这个函数是非常资源密集型的。为每个函数调用节省时间和其他资源，并为后续函数调用从保存的位置返回输入结果`1`怎么样？

这可以通过记忆来实现。

## 记忆‘双’功能

在`doubleLogged`上使用我们的`memoize`函数，

```
const doubleLoggedMemoized = memoize(doubleLogged); 
```

Enter fullscreen mode Exit fullscreen mode

我们得到一个记忆函数，它有效地加倍了输入。

```
[1, 2, 3, 1, 5, 1].forEach(n => console.log(`Double of ${n} is ${doubleLoggedMemoized(n)})); 
```

Enter fullscreen mode Exit fullscreen mode

那么，`doubleLogged`函数这次被调用了多少次呢？

当然，函数只被调用了`4`次，而不是`6`次。至于后续调用，我们的记忆化函数知道输入`1`的输出，并简单地从缓存中返回输出。

记忆像`double`这样简单的函数相当容易。记忆一个类似`factorial`的递归函数怎么样？

## 记忆‘阶乘’函数

回到添加了日志的旧函数`factorialRecursive`,

```
const factorialLogged = n => {
    console.log(`Calculating factorial of ${n}`);

    return (n < 2)
        ? 1
        : (n * factorialLogged(n - 1));
}; 
```

Enter fullscreen mode Exit fullscreen mode

并且在用重复值调用它的情况下，

```
[2, 6, 2].forEach(n => console.log(`Factorial of ${n} is ${factorialLogged(n)}`)); 
```

Enter fullscreen mode Exit fullscreen mode

你认为这个函数被调用了多少次？

该函数不是被调用`3`，而是被调用`10`次。

这是因为我们的函数是一个递归函数，它调用较小的输入。

让我们对`factorialLogged`应用我们的`memoize`函数。

```
const factorialLoggedMemoized = memoize(factorialLogged); 
```

Enter fullscreen mode Exit fullscreen mode

并使用相同的重复值进行试验:

```
[2, 6, 2].forEach(n => console.log(`Factorial of ${n} is ${factorialLoggedMemoized(n)}`)); 
```

Enter fullscreen mode Exit fullscreen mode

你觉得现在有多少次了？

叫做`8`倍。

如您所见，第二次没有使用输入`2`调用它。

然而，在计算值`6`的阶乘时，它必须计算值`5`、`4`、`3`、`2`和`1`的阶乘。

实际上，不应该再次调用值`2`和`1`的函数，因为之前已经计算了这两个值的输出。然而，事实并非如此。

你能猜到原因吗？答案很简单，我们的`memoize`函数只能记忆对`factorialLogger`的外部调用。一旦外部函数被调用，它就没有控制权了。

如果再次为相同的值调用外部函数，它会找到以前缓存的输出。然而，对于从内部对自身的任何调用，它不会被指示使用缓存，而只是继续对自身进行更多的调用。

## Y-Combinator 化险为夷！

不能*正确地*记忆递归函数的问题可以使用我们之前导出的 Y-Combinator 来解决。

为此，我们需要准备一些东西，我们将一次安排一件。

我们需要一个步进器，我们将在其中添加日志，以便知道它何时被调用(以及被调用多少次)。

```
const factorialStepperLogged = nextStep =>
    n => {
        console.log(`About to run step for ${n}`);

        return (n < 2)
            ? 1
            : n * nextStep(n - 1);
    }; 
```

Enter fullscreen mode Exit fullscreen mode

我们需要创造一个`memoize`发电机。

```
const generateMemoizer = () => {
    const cache = {};

    return func =>
        input => {
            if (!cache[input]) {
                cache[input] = func(input);
            }

            return cache[input];
        };
    }; 
```

Enter fullscreen mode Exit fullscreen mode

这个生成器只是生成一个记忆器来记忆一个函数，而不是自己去做。

这是因为我们不希望每次调用包装器时都重新初始化缓存。

我们需要修改 Y-Combinator 函数来接受 memoizer 函数。

```
const yCombinatorMemoizable = (stepper, wrapper) =>
    (
        f => f(f)
    )(
        nextStep => n => wrapper(stepper(nextStep(nextStep)))(n)
    ); 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，组合器现在采用了一个额外的函数来包装对我们的步进器的实际调用，以便仅当特定输入的输出未知时才调用它。

包装器(它是一个 memoizer 函数)为之前接收到的任何输入返回来自缓存的输出。

结合我们所拥有的一切:

```
const factorialM = yCombinatorMemo(factorialStepperLogged, generateMemoizer()); 
```

Enter fullscreen mode Exit fullscreen mode

我们用阶乘步进器和即时生成的记忆器来提供我们修改的组合函数。

所以有一个记忆阶乘函数，它可以存在于不能直接递归的环境中。

## 参考文献

*   [终于理解了 Y 组合子——一个程序员友好的视角(hisham.hm)](http://hisham.hm/2011/04/04/understanding-at-last-the-y-combinator-a-programmer-friendly-perspective)
*   更多的我不记得了，因为这篇文章是基于我的一个非常老的演示