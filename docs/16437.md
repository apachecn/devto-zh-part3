# 让你在 2019 年的编程生活充满活力的基本原则。

> 原文：<https://dev.to/sandipmavani/-basic-principles-that-will-make-your-programming-life-in-2019--1e9l>

我见过只有编写代码的程序员才明白它是如何工作的。然而，当一个人要求程序员解释代码做什么时，他或她只会说“只有上帝知道”。

这就是为什么我想讨论一些基本的(但非常有力的)原则，它们会让你的生活(和你的大多数合作者的生活)变得更容易。

## 1。**干——不要重复自己**

您必须尝试在一段代码中维护系统功能的行为。另一方面，当干的原则没有被遵循时，这被称为湿的解决方案，这意味着要么把所有的东西都写两遍，要么我们喜欢打字。干编程是非常有用的，尤其是在大型应用程序中，大量程序员不断地维护、更改和扩展代码。

枯竭代码的一个常见方法是用函数包装我们重复的逻辑，并用函数调用替换它出现的所有地方。

示例(代码重复):

```
//flow A

let username = getUserName();
let email = getEmail();
let user = { username, email };
client.post(user).then(/*do stuff*/); 
```

Enter fullscreen mode Exit fullscreen mode

```
 //flow B
let username = getUserName();
let email = getEmail();
let user = { username, email };
client.get(user).then(/*do stuff*/); 
```

Enter fullscreen mode Exit fullscreen mode

干例:

```
function getUser(){
    return {
        user:getUserName();
        email:getEmail();
    }
}
//flow A
client.post(getUser()).then(/*do stuff*/ );
//flow B
client.get(getUser()).then(/*do stuff*/); 
```

Enter fullscreen mode Exit fullscreen mode

## **2。接吻——保持简单愚蠢**

你的代码越简单，将来维护起来就越简单。这对于将来需要检查您的代码的任何人来说都是非常有用的。

保持简单出奇的难。通常当有人试图过度设计问题的解决方案时。例如，一位架构师建议为一个简单的网站创建一个微服务框架。

## **3。YAGNI——你不会需要它的**

有时候，作为开发人员，我们试图提前考虑，考虑项目的未来，编写一些额外的特性，“以防我们需要它们”，或者想“我们最终会需要它们”。就一个字:错！你不需要它，你不需要它，在大多数情况下…“你不会需要它”。

KISS 的同父异母兄弟。保持简单的一部分是不添加我们实际上不需要的模块、框架和依赖项。