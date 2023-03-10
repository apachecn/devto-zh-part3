# 最复杂的 JavaScript Fizz Buzz 解决方案

> 原文：<https://dev.to/daleljefferson/the-most-convoluted-javascript-fizz-buzz-solution-3777>

找到正确的分解级别有时是具有挑战性的。在本文中，我将把分解发挥到极致，创建一个非常复杂的 Fizz Buzz 解决方案，并分享我对正确的分解级别的感受。

## 嘶嘶嗡鸣测试

> 写一个程序打印从 1 到 100 的数字。但是对于三的倍数打印“嘶嘶”而不是数字，对于五的倍数打印“嗡嗡”。对于同时是 3 和 5 的倍数的数字，打印“FizzBuzz”。

```
const modulo = n => a => a % n;
const equals = a => b => a === b;
const compose = (...a) => x => a.reduceRight((p, fn) => fn(p), x);
const equalsZero = equals(0);
const isDivisibleBy = a =>
  compose(
    equalsZero,
    modulo(a)
  );
const isDivisibleBy3 = isDivisibleBy(3);
const isDivisibleBy5 = isDivisibleBy(5);
const isDivisibleBy15 = isDivisibleBy(15);
const ifElse = (condition, onTrue, onFalse) => n => {
  return condition(n) ? onTrue(n) : onFalse(n);
};
const identity = x => x;
const always = value => () => value;

const fizzBuzz = ifElse(
  isDivisibleBy15,
  always("FizzBuzz"),
  ifElse(
    isDivisibleBy3,
    always("Fizz"),
    ifElse(isDivisibleBy5, always("Buzz"), identity)
  )
);

for (let index = 1; index <= 100; index++) {
  console.log(fizzBuzz(index)); // 1, 2, Fizz, 4, Buzz
} 
```

我希望对读者来说很明显，我已经把分解做得太过了，问题是我应该什么时候停止，以及我如何知道停止。

> 完美的实现，不是当没有更多可以添加的时候，而是当没有什么可以拿走的时候。-安托万·德圣埃克苏佩里

让我们一步一步来。

## 起点

```
const fizzBuzz = n => {
  if (n % 15 === 0) {
    return "FizzBuzz";
  } else if (n % 3 === 0) {
    return "Fizz";
  } else if (n % 5 === 0) {
    return "Buzz";
  } else {
    return n;
  }
}; 
```

非常简洁的代码，通过将可分检查提取到它自己的函数中，我们可以使它更容易阅读和删除重复的逻辑。

## 先重构

```
const isDivisibleBy = (a, n) => n % a === 0;

const fizzBuzz = n => {
  if (isDivisibleBy(15, n)) {
    return "FizzBuzz";
  } else if (isDivisibleBy(3, n)) {
    return "Fizz";
  } else if (isDivisibleBy(5, n)) {
    return "Buzz";
  } else {
    return n;
  }
}; 
```

现在我们有了可重用的 isDivisibleBy 函数，但是我们应该进一步分解它吗？做两件事能把一整除吗？求余数和比较。

## 第二次重构

> 提取直到你倒下。鲍勃叔叔

一件事:提取到你滴下为止。

```
const equalsZero = n => n === 0;
const modulo = (a, n) => n % a;
const isDivisibleBy = (a, n) => equalsZero(modulo(a, n));

const fizzBuzz = n => {
  if (isDivisibleBy(15, n)) {
    return "FizzBuzz";
  } else if (isDivisibleBy(3, n)) {
    return "Fizz";
  } else if (isDivisibleBy(5, n)) {
    return "Buzz";
  } else {
    return n;
  }
}; 
```

我们应该现在停下来还是继续前进？你们中有多少人“掉队”了？

## 一看就知道

> 今天，我不打算进一步定义我所理解的那种速记描述中所包含的材料[“硬核色情”]，也许我永远无法明白地做到这一点。但我一看就知道，本案涉及的电影不是那个。波特·斯图尔特法官

就我个人而言，当我发现重构后的代码变得比原始代码更复杂、更难阅读时，我会停止或恢复重构。我相信这发生在第二次重构的简化示例中。像软件开发中的许多事情一样，这是个人偏好，这个代码的所有版本都工作得很好，实际上，我会想象起点代码是最快的。

在评论里让我知道你的想法，你分解到什么程度了？