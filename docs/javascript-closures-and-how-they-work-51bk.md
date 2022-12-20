# JavaScript 闭包，以及它们如何工作

> 原文：<https://dev.to/canderson93/javascript-closures-and-how-they-work-51bk>

闭包，也是最受欢迎的面试问题，是 JavaScript 中最令人困惑的部分之一。

尽管有大量文章解释闭包，但 reddit 和其他论坛上仍有许多帖子在寻求关于闭包的更多帮助。不难看出为什么，因为大多数对闭包的解释都是这样的:

> 想象你有一个背包。
> 
> *那个背包里面有一个文件夹、一个饭盒和一些邮件。*
> 
> *饭盒里面，还有一个苹果。*
> 
> *突然，所有这些物品都活了过来，并试图从彼此身上拿东西，打破了这种类比。*
> 
> *幸运的是，因为封闭，有些不能。*
> 
> *结束。*

闭包是 JavaScript 的一个基本部分，每个严肃的程序员都应该对它了如指掌——一旦你去掉了基于午餐的解释——如果你对作用域有基本的理解，掌握闭包就很容易了！

这篇文章将涵盖闭包到底是什么，如何使用它们，以及为什么您应该关注它。

## 到底什么是闭包？

长话短说:*闭包是函数。*

就是这样。说实话。显然，它们比那要复杂一点，否则我们也不会费心给它起一个特殊的名字。

闭包的概念之所以存在，是因为 Javascript 中有一条规则:**内部作用域能够访问父作用域中的所有内容。由于函数创建了一个新的作用域，这可以变成:“每个函数都可以从它的父函数访问变量和参数。”**

详细的解释是,*闭包是 Javascript* 的一个实现细节——为了确保来自父作用域*的变量保持在作用域*内，函数需要保持对它们的引用。**闭包是函数和函数存储的作用域的组合。**

对于开发人员来说，闭包意味着我创建的任何函数都只会引用它被定义的作用域，而不管这个函数实际上是在哪里被调用的。

## 我们应该如何使用闭包？

闭包的真相是你很少需要显式地“使用”它们。它们构成了函数如何处理作用域的核心部分，所以你倾向于不用考虑就使用它们。

您可能熟悉的一个常见的闭包例子是*回调。*

```
//foo.js
import {sillyConcat} from './bah.js';

const globals = {};

sillyConcat('hello', 'world' , function(result) {
    //This function creates the closure, and includes a reference to globals
    globals.hello = result;
});

//bah.js
function sillyConcat(s1, s2, callback) {
    //This is where the closure is called - there is no direct access to
    //variables from foo.js, but the function runs fine anyway
    callback(s1 + s2);
}

export {
    sillyConcat: sillyConcat
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么我们需要了解闭包？

在大多数情况下，你不知道。*除了你做*的时候。了解*函数如何在父作用域中存储对变量的引用以避免错误和一些棘手的问题是很重要的。*

这是一个涉及闭包的常见问题(也可能是一个面试问题)。

```
function delayedPrint() {
    let total = 0;
    for (let i = 0; i < 4; i++) {
        total += i;
        setTimeout(function closure() {
            console.log(total);
        }, 200);
    }
}

delayedPrint(); //expected: 0, 1, 3, 6 actual: 6, 6, 6, 6 
```

Enter fullscreen mode Exit fullscreen mode

发生这种情况是因为我们的每个`setTimeout`函数都引用了`total`变量，但是不检查它的值。当函数被调用时，循环已经运行完毕，并且`total`等于 6——所以每个函数都输出`6`。

为了解决这个问题，我们需要将`total`的值复制到一个不存储在父范围内的新变量中。我们可以通过将它作为参数传递给函数来做到这一点。

```
function delayedPrint() {
    let total = 0;
    for (let i = 0; i < 4; i++) {
        total += i;
        setTimeout(function closure(total) {
            console.log(total);
        }, 200, total);
    }
}

delayedPrint(); //expected: 0, 1, 3, 6 actual: 0, 1, 3, 6 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以通过创建另一个函数并立即调用它(一个生命)来实现这一点。

```
function delayedPrint() {
    let total = 0;
    for (let i = 0; i < 4; i++) {
        total += i;
        (function(total) {
            setTimeout(function closure() {
                console.log(total);
            }, 200);
        })(total);
    }
}

delayedPrint(); //expected: 0, 1, 3, 6 actual: 0, 1, 3, 6 
```

Enter fullscreen mode Exit fullscreen mode

如果您有兴趣了解更多关于闭包的知识，MDN 有一篇很棒的文章。

如果你觉得这篇文章有帮助，[考虑加入我的时事通讯](https://pages.convertkit.com/de362bddba/e411d0e335)。