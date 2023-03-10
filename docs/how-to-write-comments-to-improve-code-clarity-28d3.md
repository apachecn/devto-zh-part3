# 如何编写注释来提高代码的清晰度

> 原文：<https://dev.to/canderson93/how-to-write-comments-to-improve-code-clarity-28d3>

我不打算撒谎——我曾经很难写评论。我浪费了几个小时去写对任何人都没有帮助的注释，却忽略了那些花费我几个小时调试时间的注释。

当我们学习编码时，我们被教导要在早期写注释。在 FreeCodeCamp 的课程中，“如何编写 JavaScript 注释”直接排在“JavaScript 入门”之后。

我们没有学到的是*何时*写评论。不幸的是，知道什么时候评论并没有严格的规则，但是我已经编辑了一些关于你应该写什么类型的评论以及什么时候写的指南。

## 文档注释

第一种类型的注释是文档注释——这意味着在高层次上捕捉你的软件，并提供对其结构和功能的洞察。

重要的是，这些注释还捕捉到了**意图**，因此您能够比较代码做了什么和它想要做什么。

文档注释是以工具友好的格式编写的，以便以后可以提取到文档页面中。在 JavaScript 中，[这个](http://usejsdoc.org/)我们倾向于用 JSDoc。

这些注释需要是独立的，并且在不阅读代码的情况下有意义。

### 如何写出有用的文档注释:

文档注释需要捕捉关于代码的两个主要细节。

1.  代码是做什么的？
2.  代码应该如何使用？

**代码做什么**是一个高层次的概述。最重要的一点是，*我们不想知道实施细节——*只是一个高层次的看法。

就像用 TDD 编写测试一样，在编写代码之前编写这些注释通常是个好主意。这有助于防止不必要的细节出现(因为它们还不存在)。

**代码应该如何使用**通常包括你期望你的输入是什么样的(比如用 JSDoc `@param`标签)，如果适用的话，你会(或不会)使用代码的情况。

即使你不打算向他人公开你的代码，你也应该写下这些注释——让每个人都知道整个代码库是不现实的，所以这些注释为你的同事(以及未来的你)提供了有用的指导。

```
/**
* Concatenates and returns the values of an array.
* This function has been deprecated. Use Array.concat instead.
*
* @param {array.<string>} arr an array of strings
*
* @return {string} concatenated array
* @deprecated
*/
function concatArray(arr) {
    // ... do something
} 
```

Enter fullscreen mode Exit fullscreen mode

**何时撰写文档注释**

编写文档注释的规则很简单:写在类的顶部，解释类代表什么；和函数的顶部来解释这个函数的作用。

唯一不需要写文档注释的时候(但无论如何都可能)是当函数覆盖父函数的时候——你可以用父函数的注释来表达你的函数。

第二种类型的注释是澄清注释。这些是您可能更熟悉的注释——这些注释是解释代码的*为什么*的内联注释。

获得正确的澄清评论是很难的，因为通常没有客观正确的答案。总的来说，你想尝试捕捉那些*不会立即显现出来的细节*。这种获取可以分为两种类型:

1.  解释*为什么*某些代码存在
2.  解释一段令人困惑的代码在做什么

下面是一个不好的澄清评论的例子:

```
function concatArray() {
    //Assign values to array
    let arr = [1, 2, 3, 4, 5];
    let total;
    //loop through values
    for (let i = 0; i < arr.length; i++) {
        //if total doesn't exist, then set a value, otherwise add
        //them together
        total = total ? arr[i] : total + arr[i];
    }
    //return the total
    return total
} 
```

Enter fullscreen mode Exit fullscreen mode

我们怎么知道这些是不好的评论？如果注释被删除，我们对准则的理解不会改变。这不需要额外的解释。

我们来对比一下这样的东西:

```
function addSetEntry(set, value) {
    // Don't return set.add because it's not chainable in IE11
    set.add(value);
    return set;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个评论并不吸引人，但它确实提供了我们还没有的信息。如果以后再来，我们就要重新考虑直接返回`set.add(value)`的“改进”了。

### 如何写出有用的澄清评论

让澄清评论变得有用的方法非常简单:加入缺失的信息。诀窍是知道*什么时候*遗漏的信息足够好，值得评论。

**何时写注释解释代码存在的原因**

你通常不会在第一次尝试时写这些，除非你第一次使用了一个不明显的解决方案。关于你何时应该写这些评论的简单规则是*每当你做一些你通常不会做的事情的时候-* 一段额外的代码来修复一个错误；你对解决方案的第二次尝试等。

```
/* 
don't use the global isFinite() because it returns true for null values
*/
Number.isFinite(value)

/* A binary search turned out to be slower than the Boyer-Moore algorithm
   for the data sets of interest, thus we have used the more complex, but
   faster method even though this problem does not at first seem amenable
   to a string search technique. */ 
```

Enter fullscreen mode Exit fullscreen mode

事实上，这些是最难说对的评论，但它们是救命稻草。你不倾向于写太多这样的文章，所以你应该尽可能的写这些。

如果你不确定，*写下评论*——你可以随时删除它。

**何时写评论解释易混淆代码**

在理想的情况下，写注释解释令人困惑的代码的最佳时机是*永不。*写这条注释最有用的方法是*重构你的代码，这样它就不需要注释了。*

举这个例子:

```
function calculateDirection() {
    // .. some code here
    let directionCode = 0; // none
    if (x > 0 && position != x) {
        if (0 > x - position) {
            directionCode = 1; //left
        } else if (0 < x - position) {
            directionCode = 2; //right
        }
    } 
    // .. some more code
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码很混乱，这是毫无疑问的。但是我们可以重构它，而不是用注释做修补工作。

```
const DIRECTION = {
    NONE: 0,
    LEFT: 1,
    RIGHT: 2
};

function calculateDirection() {
    // .. some code here

    let directionCode = DIRECTION.NONE;
    if (x > position) {
        directionCode = DIRECTION.LEFT;
    } else if (x < position) {
        directionCode = DIRECTION.RIGHT;
    }

    // .. some more code
} 
```

Enter fullscreen mode Exit fullscreen mode

或者如果你喜欢更紧凑的东西:

```
const DIRECTION = {
    NONE: 0,
    LEFT: 1,
    RIGHT: 2
};

function calculateDirection() {
    // .. some code here

    let directionCode = x > position ? DIRECTION.LEFT :
                        x < position ? DIRECTION.RIGHT :
                                        DIRECTION.NONE;

    // .. some more code
} 
```

Enter fullscreen mode Exit fullscreen mode

可悲的是，这不是一个理想的世界，而且你不可能总是重构你的代码使其完全可理解。

因此，关于何时加入注释的真正答案是*当代码难以阅读时。如果别人不能在 5 秒钟内说出你的代码块做了什么，那就需要一个注释。*

这里真正的诀窍是，当你刚刚写完代码时，你是判断某个东西是否难读的最差的人。在决定是否写评论时，你需要设身处地为你的队友着想。

和以前一样，*添加评论比较稳妥。有一个不必要的注释比需要注释但没有注释的代码好得多。*

如果你正在学习 web 开发，并且想提高你的编码水平，考虑加入我的邮件列表。T3】