# 通过翻译学习

> 原文：<https://dev.to/ayelishaamoah/learning-by-translation-1759>

由于软件工程师面临的变化速度，掌握新的语言和技术是至关重要的。创客学院的重点之一是能够自信地说“我可以制造任何东西”，对我来说，这意味着能够对不熟悉的技术感到舒适，这样你就不会在整个职业生涯中局限于同一种技术。

在 Makers Academy 的第 5 周，我们讨论了通过翻译学习的概念，这涉及到用一种你以前处理过的问题，并试图用一种不熟悉的语言来解决这个问题。

通过关注直接移植到另一种语言中的编程概念和模式，可以帮助快速接触到一种新的语言。

一旦你有了想要解决的问题的想法，记下为了解决这个问题你需要理解的概念。例如，其中一个步骤涉及编写 For 循环:

#### 红宝石代码

```
for variable in expression do
  some repeatable statement
end 
```

Enter fullscreen mode Exit fullscreen mode

```
for i in 0..5
   puts "Number: #{i}"
end 
```

Enter fullscreen mode Exit fullscreen mode

#### JavaScript 代码

```
 for (variable; condition; increment) {
  repeatable statement
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
var i;

for (i = 0; i < 5; i++) {
 console.log("Number: " + i);
} 
```

Enter fullscreen mode Exit fullscreen mode

FizzBuzz 是熟悉一门新语言的一个很好的开始挑战，因为它是一个简单的问题，使用了核心编程概念，如循环，并可用于学习一个新的测试框架。

虽然这是一种很有用的方法，可以快速了解一门语言的一些常用功能，你知道你会需要这些功能，但它不一定能帮助你理解这门语言的约定，例如 Ruby 约定建议在 snake_case 中为方法和变量命名变量，但 JavaScript 约定更喜欢 camelCase。