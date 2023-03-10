# 单一状态应用程序中的不可变数据管理

> 原文：<https://dev.to/basal/immutable-data-management-in-single-state-applications-56ce>

在这篇文章中，我将探索不可变数据和相关 JavaScript 数据类型的应用程序状态管理。我将关注流行的 Redux 库的“Reducer ”,它以应用程序状态管理而闻名。

## 什么是状态管理

最近，状态管理的概念已经在单页应用程序开发中扮演了重要的角色。有些人会说 Redux 是激发我们行业概念的库。许多开发人员都会同意，它允许他们以简单的方式创建复杂的应用程序，并且更容易看到应用程序的当前状态。因此，我们的代码更容易调试。尽管它是如此强大；Redux 本身是用非常简单的概念构建的。

Redux 给 Javascript 应用带来了哪些简单性？我想把重点放在 Redux 中包含的重要元素之一“Reducer”上。Reducer 是唯一一个使用纯函数更新状态的接口。为了更新状态，Reducer 获取当前状态和称为 Action 的差异数据，并生成一个新状态。

[![reducer](img/af93dcd7906f1b69989f820bb10488b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iGD1IN9F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mo1qiuni4luwbd8da98a.png)

这里我特别提到的是 Reducer 并不直接更新状态，而是生成一个新的状态。生成的状态独立于更新前的状态。这意味着在生成一个新状态后，以前的状态不会改变。记住这一点，它使我们能够轻松地跟踪应用程序在任何给定的时间序列内的状态变化。一般这种生成后不能更改的数据称为**“不可变”**。

[![state transition](img/d822500be9b03374e9fa1485aa973e23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NKfdOqdV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0vdwfmz52zvo9wm38w5y.png)

## JavaScript 中不可变的数据

在进入正题之前，我想简单提一下 JavaScript 中的不可变数据。原始类型数据(如字符串、数字和布尔值)在创建后不能更改。另一方面，引用类型数据(如对象和数组)可以在创建后更新。那些被称为**【易变】**。

`var a = 1; var b = { k: 1 }; var c = [1]; a = 2; // this is not changing 1 but creating 2 newly and re-assigning to a b.k = 2; // can change a property c[0] = 2; // can add new value console.log(b); console.log(c);`

看上面的例子，我们可以看到对象`{ k: 1 }`和数组`[1]`是可以改变的。您可能会看到，分配给变量“a”的数字也是可变的，但是，这不是改变“1”本身，而是创建“2”并将其重新分配给“a”(变量“a”的值已被“2”覆盖)。

`var b = { k: 1 }; var c = [1]; Object.freeze(b); Object.freeze(c); b.k = 2; // this doesn't work c[0] = 2; // this doesn't work console.log(b); console.log(c);`

有一种方法可以使对象和数组不可变。那就是用 **Object.freeze()** 。在上面的示例中，通过应用 Object.freeze()使对象和数组的值不可更改。

## 不可变的变量

ES6 中新增了变量定义语句“const”和“let”。通过使用 const 定义一个变量，我们可以定义一个不能被重新赋值的变量。在下面的例子中，如果你试图给用 const 定义的变量 b 重新赋值，你会得到一个错误。这样，我们就可以在意外的变量重分配发生之前防止它。

`let a = 1; a = 2; // You can assign 2 to a const b = 1; b = 2; // You cannot assign a value (Throw an error)`

当用 const 定义一个对象时，我们应该注意的是对象的属性仍然是可变的。Const 只是禁止变量的重新赋值，并没有使对象不可变。为了使一个对象不可变，我们需要应用 Object.freeze()。

`const c = { k: 1 }; c.k = 2; // You can change the property c.l = 3; // You also can add a new property console.log(c); const d = { k: 1 }; Object.freeze(d); d.k = 2; // You cannot change the property d.l = 3; // You also cannot add a new property console.log(d);`

## 在嵌套结构中

我已经介绍了 Object.freeze()作为一种使对象或数组不可变的方法。实际上，我需要告诉你一件事，你在处理嵌套结构时需要注意。让我们看看下面的例子。我们可以看到，即使我们应用 Object.freeze()，我们仍然能够改变嵌套对象的值，它只对对象有效，对嵌套结构无效。这是因为 Object.freeze()只对传递给它的顶级对象有效。如果想让嵌套结构的所有值都不可变，就必须对每个对象应用 Object.freeze()。

`const a = { k: { l: 1 } }; Object.freeze(a); a.k = { l: 2 }; // You cannot change it but... console.log(a.k); a.k.l = 2; // You can change the child value console.log(a.k); const b = { k: { l: 1 } }; Object.freeze(b); Object.freeze(b.k); a.k.l = 1; // You cannot change the value! (It's still 1) console.log(a.k);`

## 不可变应用状态管理用减速器

现在，让我们进入主题，状态管理。我将深入探讨 Redux 的重要元素 Redux 的实现。

[![reducer](img/af93dcd7906f1b69989f820bb10488b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iGD1IN9F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mo1qiuni4luwbd8da98a.png)

如上面的表达式所示，Reducer 通过接收当前状态和动作来更新状态，当前状态和动作是实际的变化。在这个过程中，Reducer 生成一个新的独立状态，而不改变当前状态。假设我们将当前状态作为对象`{ a: 1, b: 1, c: 1 }`，将动作作为对象`{ b: 2 }`。Reducer 生成一个新的状态，并将动作 2 赋予新状态的“b”。因为“a”和“c”没有改变，所以这些值被复制并按原样设置为新值。结果，对象`{ a: 1, b: 2, c: 1 }`作为新的对象返回。通过用当前文件替换新文件来完成更新。因此，Reducer 通过在每次状态更新时生成一个新状态，使我们能够以一种不可变的方式管理我们的应用程序状态。

[![state update](img/0b9c70ca356c0acaf3a61b72ac0a27db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8lsCTQuQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pmhif55qcvsdmhq3dbb8.png)

## 减速器实现示例

下面的示例代码是根据上面的图表编写的。正如你所看到的，Reducer 有一个非常简单的结构，只生成和返回一个用当前状态和动作创建的新状态。在第 3 行，它通过用当前状态和动作的顺序覆盖传递给 Object.assign()的第一个参数的新对象，将值返回给调用者。由于在那里生成的状态值存储在与当前状态不同的内存空间中，当我们在第 17 行比较它们时，我们可以确认新状态和前一个状态之间的差异。正如您可能已经在第 15 行中意识到的，我们仍然可以像`state.a = 2`一样无意中直接改变生成的状态，但是，当与下一个状态交换时，那些改变将会完全丢失。因此，状态更新总是必须由 Reducer 来完成。

`function reducer(state, action) { // Generate new state return Object.assign({}, state, action); // or use spread operator of es6 like this // return { ...state, action }; } // Initialize state let state = { a: 1, b: 1, c: 1 }; // Create action const action = { b: 2 }; // Cache previous state const prevState = state; // Update state state = reducer(state, action); console.log(state === prevState); // false console.log(state.b); // 2`

## 结论

我介绍了具有不可变数据和相关 JavaScript 数据类型的应用程序状态管理，同时重点介绍了 Redux 的状态更新功能“Reducer”。在开始，我提到了不可变数据带来的简单性。不可变数据是生成后不可改变的数据，如数字和字符串类型。此外，还提到对象和数组类型被称为可变的。除此之外，我提到过通过申请 Object.freeze()可以使那些可变的数据变得不可改变。最后，我用一个例子介绍了 Reducer 如何在一个不可变的中生成一个新的状态。这一次，我有意使用非常简单的状态数据作为一个易于解释的例子，然而，实际状态看起来会更复杂，就像使用嵌套一样。我将在我的另一篇文章中介绍如何通过保持不可变来管理这种状态。