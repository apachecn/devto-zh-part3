# 如何在编码面试中解决挑战

> 原文：<https://dev.to/adyngom/how-to-approach-solving-a-challenge-during-a-coding-interview-1gf4>

像生活中的许多事情一样，精通需要实践，编码面试也不例外。然而，很多时候，焦点是试图从一开始就找到最佳解决方案，而不是如何迭代并可能到达那里的方法。

依我拙见，最重要的部分是先把它做好。在您正常的编码生活中，如果没有经过一系列迭代，您很少能够产生“完美”的代码。

编码面试中的方法不应该有所不同，如果表现得好，应该有助于您在展示您的问题解决技能时获得宝贵的分数。

如果你被要求解决著名的 FizzBuzz 挑战，我将带你经历一次你和面试官之间的真实对话。

### 挑战

写一个程序打印从 1 到 100 的数字。但是对于三的倍数而不是数字印刷嘶嘶声和对于五的倍数印刷嘶嘶声。对于 3 和 5 的倍数的数字，打印 FizzBuzz

### 上下文

FizzBuzz 挑战并不是 JavaScript 特有的，几乎在每一种编程语言中都是编码面试过程的一部分。这通常是评估候选人基本编程本能的快速检查，但如果面试官决定这样做，也可以变成对深入知识的评估。这通常是在屏幕共享时进行的轻量级首次技术面试的一部分。非 JavaScript 程序员也喜欢询问并快速评估您的技术知识和方法。

在一个 Javascript 环境中，期望演示对以下一些或所有概念的熟悉程度:

*   逻辑运算符
*   环
*   虚伪的价值观
*   三元运算符
*   类型强制

### 走近

就像你可能遇到的任何问题一样，即使是那些看起来很熟悉的问题，认真阅读并分解成小部分也是必须的。向面试官明确，你需要 3 到 5 分钟的时间冷静阅读，并提出重写你的理解。

如果你乐意大声说出那部分，那就更好了。例如，我可能会这样重写:

*   所以从 1 到 100 记录控制台编号——我需要一个循环
*   对于 3 的倍数，输出字符串“Fizz ”,而不是数字
*   对 5 的倍数做同样的操作，输出为“嗡嗡声”
*   如果数字是 3 和 5 的倍数，那么输出‘fizz buzz’——如何检查 a 是否是 b 的倍数？？
*   如果以上所有情况都失败，那么就直接输出数字

我可能会问面试官，我是否应该担心边缘情况或错误的输入。这通常意味着输入将是正确的，边缘情况可能是不必要的。不过，你提出问题的事实为你解决问题的方式增加了一点口才。

## 解决方案

有一点很关键，也值得实践，那就是在面试过程中，当你构建解决方案时，带着这个人走一遍你的步骤。从显而易见的开始，您可能需要一个函数或类作为您的主要构造。从这里开始，永远记住 K . I . a . s . a . p:)原则——尽可能简单

### 第一步

```
// comments are me talking out loud
// let's build the function structure
function fizzBuzz( start = 1, end = 100) { // default parameters to set the default range
    // I need a loop - let's go with for
    for( let i = start; i <= end; i++) {
        // probably a variable for what will be outputted
        let output = i;

        // rest of the logic here

        // outputting the result
        console.log(output);
    }
}
// call the function
fizzBuzz(); // this prints out 1 to 100 - fancy ;) 
```

以上满足了我重写挑战理解的第一个目标

### 第二步

现在，如果我跟随挑战的节奏，我将解决两件事:

```
- Choosing the proper operator to find if a number is a multiple of another
- Apply it for the multiple of 3 condition and output 'Fizz' 
```

**余数操作符- %** ，是这里最完美的工具。如果数量 **a** 是数量 **b** 的倍数，那么

```
( b % a) === 0; // will be true;
// 4 is a multiple of 2
( 4 % 2 ) === 0; // is true 
```

让我们在函数体中应用这个

```
// rest of the logic here
if( (i % 3) === 0 ) {
    output = 'Fizz';
}
// Knowing that 3,6 and 9 are multiple of 3 let's
// quickly test a small sequence by calling

fizzBuzz(1,10); 
// this should output
// 1, 2, 'Fizz', 4, 5, 'Fizz', 7, 8, 'Fizz', 10 
```

### 最后一步

既然嘶嘶声条件运行完美，我们现在可以将同样的逻辑应用于其余的部分

```
// multiple of 5
if( (i % 5) === 0 ) {
    output = 'Buzz';
}

// multiple of 3 and 5
if( (i % 3) === 0  && (i % 5 === 0)) {
    output = 'FizzBuzz';
} 
```

Wowza！！这满足了所有的条件，给了我们这个解决方案的主厨，一旦组合
并去掉所有的注释

```
function fizzBuzz( start = 1, end = 100) { // default parameters to set the default range
    for( let i = start; i <= end; i++) {
        let output = i;
        if( (i % 3) === 0 ) {
            output = 'Fizz';
        }
        if( (i % 5) === 0 ) {
            output = 'Buzz';
        }
        if( (i % 3) === 0  && (i % 5) === 0) {
            output = 'FizzBuzz';
        }
        console.log(output);
    }
}
fizzBuzz(); 
```

现在，我有了一个满足挑战请求的工作解决方案。接下来的事情在面试中是非常微妙的。我的代码有问题。检查 **3** 和 **5** 倍数的最后一个 **if 块**似乎是多余的。

现在，我是应该大声说出来并建议重构它，还是应该等待面试官大声说出来？

面试是关于管理时间和最大化你的优缺点。如果你觉得自己非常有信心在可控制的时间内拿出更可靠的东西，那就去做吧。如果有疑问，等待被询问。

这样，面试官就认为你剩下的时间可能值得更深入地探讨这个问题。

如果决定关注重构会很有趣，这可能是一种接近重构步骤的方法

### 重构

当然，对于这个特殊的挑战，我们可以使用一个奇特的一行程序，但是我不是一个特别喜欢为了奇特或者漂亮而做事的人。

让我们打开开关，这次我要做的是，我将向你们展示我的最终解决方案，并向你们介绍我是如何实现的。

如果你要阅读和理解别人的代码，或者如果你要向别人解释它，这可以变成一种方便的技能。多年来，我为这个挑战提供了许多解决方案，但下面的这个是我最喜欢的。

```
function fizzBuzz( start = 1, end = 100) {
    for( let i = start; i <= end; i++) {
        let output =  ( (i % 3) ? '' : 'Fizz' ); // if multiple of 3 is falsy
        output += ( (i % 5) ? '' : 'Buzz') ; // if multiple of 5 is falsy
        console.log(output || i); // output value or i if output is falsy
    }
}
fizzBuzz(1,15); 
```

该解决方案使用三元操作符语法来设置条件，并利用了起初对于未经训练的人来说可能不太明显的东西——JavaScript falsy 值。

让我们从 falsy 值 JavaScript 开始，我们到底在谈论什么。Mozilla 开发者网络(MDN ) 提供了一个很好的定义:

> 假值是在布尔上下文中遇到时被视为假的值。JavaScript 使用类型转换将任何需要的值强制转换为布尔值，比如条件和循环。

对于我们的特定上下文，重要的关键字是**、【布尔上下文】、**、【条件句】、**，因为它们与我们的解决方案相关。在了解如何应用之前，这里列出了 Javascript 中最常见的 falsy 值:**

*   布尔型**假**与字符串***‘假’***不同
*   数字**0**——这又一次不同于字符串***‘0’***
*   **空的**对象
*   分配给未初始化变量的原始类型**未定义**
*   空字符串的任何表示形式，如单引号、双引号或反勾号。

### 重写

让我们专注于 fizzBuzz 功能的一个部分

```
if( (i % 3) === 0 ) {
output = 'Fizz';
}
// this could be refactored as
if( !(i % 3) ) output = 'Fizz'; 
```

分解重构后的行给了我们这幅图

*   如果(...)==> **外部条件构造-内部布尔上下文**
*   ！==> **为假**
*   (i % 3) ==> **类型强制-将检查值是 falsy 还是 true**

用几个数字代替 **i** 以便更好的理解

```
if (!( 1 % 3) ...) /*becomes*/ if (!( 3 ) ...) /*3 is not false or falsy so check fails*/
if (!( 2 % 3) ...) /*becomes*/ if (!( 6 ) ...) /*6 is not false or falsy so check fails*/
if (!( 3 % 3) ...) /*becomes*/ if (!( 0 ) ...) /*0 is not false but is falsy so check passes*/ 
```

我可以用上面的逻辑重写我的整个函数

```
function fizzBuzz( start = 1, end = 100) {
    for( let i = start; i <= end; i++) {
        let output = i;
        if( !(i % 3) ) output = 'Fizz';
        if( !(i % 5) ) output = 'Buzz';
        if( !(i % 3) && !(i % 5) ) output = 'FizzBuzz';
        console.log(output);
    }
} 
```

当我得到这个解决方案时，我欣喜若狂，但不幸的是，它并没有持续太久。最后一行对我来说仍然是多余的，老实说是困扰我。我怎么能一次把 3 和 5 的支票合在一起。

然后我突然想到，如果我可以从一个空字符串开始，如果它通过了 3 个条件，就给它加上单词“Fizz ”,如果它也通过了 5 个条件，就给它加上单词“Buzz”。我在一张纸上画了这个

*   i = 1 == >无嘶嘶声' ' == >无嗡嗡声' ' == >输出为 1
*   i = 3 == >是“嘶嘶”== >没有嗡嗡声' ' == >输出是“嘶嘶”
*   i = 5 == >没有嘶嘶声' ' == >是'嗡嗡声' == >输出是'嗡嗡声'
*   i = 15 = >是‘嘶嘶声’= = >是‘嗡嗡声’= = >输出是‘嘶嘶声’

三元运算符允许以非常简洁的方式在条件检查时赋值，在条件检查失败时赋值。

其他事情变得很明显，**当我们循环遍历 **i** 的值时，我们输出一个字符串或一个数字**，正如我们在前面的部分中看到的，空字符串是一个虚假值。那么，我们如何将所有的智能转化为工作代码呢？

实现这一点的关键是**输出**的值要么是可能的字符串‘Fizz’，‘Buzz’，‘Fizz Buzz’之一，要么是 falsy。在福尔西案**中，我**将被原样通过。

所以最后重写了更多的评论

```
function fizzBuzz( start = 1, end = 100) {
    for( let i = start; i <= end; i++) {
        let output =  ( (i % 3) ? '' : 'Fizz' ); // output is assigned a value or empty
        output += ( (i % 5) ? '' : 'Buzz') ; // output concatenates the next value
        console.log(output || i); // || or operator if output is falsy will show i value
    }
}
fizzBuzz(1,15); 
```

希望你遵循了所有这些:)这对我来说是一个非常令人满意的解决方案，因为我相信它易于阅读，解决了问题，并且有一点雄辩的 JavaScript 在里面。

### 最后的话

编码练习只涵盖了编码面试中发生的许多事情的一个方面。

正如我提到的步骤，不管问题有多复杂，能够交付都需要大量的实践。

不要犹豫使用模拟面试(我们将很快在 Javascript 中提供一些，稍后会提供更多)来练习它的对话方面。

我希望这是有用的，请分享并发表评论:)