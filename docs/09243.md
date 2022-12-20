# 听说过 Javascript 中的提升吗？事情就是这样。

> 原文：<https://dev.to/_ferh97/have-you-heard-about-hoisting-in-javascript-this-is-what-it-is-2a1b>

[![Coding](img/8af0a9fdcac22aba679f4dd0ad0aadd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fdws3U13--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dcko79fnp/image/upload/v1553482107/pankaj-patel-1366018-unsplash.jpg)

Javascript 是一种每当我发现新东西时都会用到的语言。

这是一个非常重要的话题，我想与你分享，以便更好地理解它。

好吧，让我们继续讨论当我们不知道某事时的典型问题。

## **什么是吊装？**

让我们知道提升这个术语的意思是举起某物。要知道，Javascript 在执行你的代码的时候，会先把你所有的变量和函数声明提升到你正在工作的代码的顶部。

那基本上就是`hoisting`。

让我们看一个代码的例子。

```
console.log(name)
var name = 'Fernando' 
```

当查看这段代码时，应用提升，你认为它会打印变量' name '的值吗？得出结论再继续看。

让我们看看结果。

```
console.log(name)
var name = 'Fernando'
// undefined => Output 
```

也许，你会问为什么，如果我们知道提升将我们的变量和函数提升到代码的其余部分之上。好吧，如果你读得好，Javascript 只会引发变量的声明而不会引发初始化。

对内，这就是吊装概念的体现。

```
var name

console.log(name)

name = 'Fernando' 
```

这样，我们清楚地看到了它打印‘未定义’的原因，因为我们将值赋给了`console.log(name).`之后的变量

> 注意:当声明变量而没有初始化时，它们默认被赋予值`undefined.`,因此，这些代码示例的输出。

正如我们前面提到的，函数也会发生同样的事情。

```
sayHi(myName)

var myName = 'Fernando'

function sayHi(myName) {
    console.log('Hello', myName)
} 
```

我们看到这个代码片段，你认为它会像我们真正期望的那样运行吗？

让我们通过应用提升来转换代码。

```
var myName

function sayHi(myName) {
    console.log('Hello', myName)
}

sayHi(myName)

myName = 'Fernando' 
```

如您所见，变量和函数的声明都移到了代码的顶部。首先是变量的声明，然后是函数。但是变量的初始化仍然在同一行代码中。知道了这些，我们就可以意识到输出会是`undefined`。

## **而 let 和 const 呢？**

其应用方式与`var`相同，但有所不同。使用`let`和`const`，而不是打印值`undefined`，将会抛出一个错误。

*   Let:它将向我们抛出一个引用错误，表明变量没有被定义。

*   Const:它会抛出一个语法错误，指出变量必须在声明的时候初始化。

## **如何避免吊装出现问题或失误？**

解决方案是在代码的开头声明变量。此外，请记住，您必须在代码中执行值之前对它们进行赋值。请记住，这取决于您的范围。也就是说，如果你在一个函数中声明了变量或函数，它们会转到函数的开头，因为这是你工作的范围。

这不仅有助于避免错误，而且有一个更干净的代码，更容易维护和其他程序员阅读。

希望你喜欢它，最重要的是，你现在对吊装有了一个清晰的概念。