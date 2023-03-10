# 我喜欢的 5 种编程模式

> 原文：<https://dev.to/thejohnstew/5-programming-patterns-i-like-53dp>

在这篇文章中，我将介绍一些我在编程时尝试使用的模式。这些模式是我最近工作时对自己的观察，也是我多年来从同事那里偷来的一些观察。

这些模式没有特定的顺序，只是一个简单的集合。

## 1。提前退场

```
function transformData(rawData) {
  // check if no data
  if (!rawData) {
    return [];
  }

  // check for specific case
  if (rawData.length == 1) {
    return [];
  }

  // actual function code goes here
  return rawData.map((item) => item);
} 
```

我将这种模式称为“提前退出”，但有些人也称之为“保镖模式”或“安全条款”。抛开命名不谈，这种模式采用的方法是首先检查无效用例，然后从该函数返回，否则它会继续执行该函数的预期用例并执行。

对我来说，这种方法有一些我非常喜欢的优点:

*   鼓励思考无效/边缘案例以及如何处理这些案例
*   避免针对意外用例的意外和不必要的代码处理
*   精神上允许我更清楚地处理每个用例
*   一旦采用，您可以快速浏览函数并理解流程和执行，这通常遵循自上而下的方法，从无效情况->小情况->预期情况

更多信息:

*   [里克·申宁克的蹦跳者图案](http://rikschennink.nl/thoughts/the-bouncer-pattern/)

## 2。切换到对象文字

```
// Switch
let createType = null;
switch (contentType) {
  case "post":
    createType = () => console.log("creating a post...");
    break;
  case "video":
    createType = () => console.log("creating a video...");
    break;
  default:
    createType = () => console.log('unrecognized content type');
}

createType();

// Object literal
const contentTypes = {
  post: () => console.log("creating a post..."),
  video: () => console.log("creatinga  video..."),
  default: () => console.log('unrecognized content type')
};

const createType = contentTypes[contentType] || contentTypes['default'];
createType(); 
```

接下来是移除`switch`。我在写每个`case`的时候经常出错，并且经常忘记一个`break`。这导致了各种有趣的问题。当我写代码时,`switch`语句并没有增加多少价值。它好像碍事了。

我更喜欢使用对象文字，原因如下:

*   不用担心`case`或者`break`
*   更容易阅读并快速了解发生了什么
*   对象文字很容易编写
*   较少代码

更多信息:

*   [开关箱，否则或 May Shavin 的循环图](https://medium.com/front-end-weekly/switch-case-if-else-or-a-lookup-map-a-study-case-de1c801d944)
*   [用 Todd 座右铭代替 switch 语句](https://ultimatecourses.com/blog/deprecating-the-switch-statement-for-object-literals)
*   [重写 Javascript:克里斯·布尔金替换 Switch 语句](https://medium.com/chrisburgin/rewriting-javascript-replacing-the-switch-statement-cfff707cf045)

## 3。一个循环两个数组

```
const exampleValues = [2, 15, 8, 23, 1, 32];
const [truthyValues, falseyValues] = exampleValues.reduce((arrays, exampleValue) => {
  if (exampleValue > 10) {
    arrays[0].push(exampleValue);
    return arrays;
  }

  arrays[1].push(exampleValue);
  return arrays;
}, [[], []]); 
```

这种模式没什么特别的，我应该很快就意识到了，但是我发现自己过滤了一个项目集合，得到了所有匹配某个条件的项目，然后对不同的条件再次这样做。这意味着在一个数组上循环两次，但我可以只做一次。

原来这有一个名字(分叉)，我从[30secondsofcode.org](https://30secondsofcode.org/#bifurcate)那里偷来的。如果你从未去过那个网站，我建议你去那里。这么多有用的信息和代码。

我知道 reduce 可能有点令人生畏，并且不太清楚发生了什么，但是如果您能够适应它，您就可以在循环集合时真正利用它来构建任何您需要的数据结构。他们真的应该把它叫做`builder`而不是`reduce`。

更多信息:

*   [30secondsofcode.org](https://30secondsofcode.org/)

## 4。没有“foo”变量

```
// bad
const foo = y && z;

// good
const isPostEnabled = isPost && postDateValid; 
```

这可能看起来有点显而易见，但我相信我们都见过这样做的代码。花点时间，尽你所能恰当地命名一些东西。

这对于工作中的专业人士或处于教育他人位置的人来说尤其重要。应该使用变量命名来帮助解释和给出代码中正在发生的事情的上下文。

有人应该能够阅读你的代码，并开始大致了解什么是试图解决的问题。

更多信息:

*   理查德·谭的《命名变量的艺术》

## 5。嵌套三角形

```
let result = null;
if (conditionA) {
  if (conditionB) {
    result = "A & B";
  } else {
    result = "A";
  }
} else {
  result = "Not A";
}

const result = !conditionA
  ? "Not A"
  : conditionB
  ? "A & B"
  : "A"; 
```

我承认，一开始筑巢的想法令人不快。这似乎是编写条件句的一种聪明方式。然后我开始编写业务逻辑，发现自己有嵌套的 if else 子句和一些非常有问题的条件逻辑。

我认为`if`和`else`更容易阅读，因为它们是真实的单词，但当这些单词嵌套在一起时，我开始很难跟上正在发生的事情，也很难记住所有事情。

我开始遵从 ternaries 和嵌套 ternaries，我发现我一眼就能明白发生了什么。

我认为这种模式真的取决于你和你的团队以及你的偏好。我曾在两方面都做得很好的代码库中工作过，并能看到这两方面，但我越来越喜欢个人嵌套术语。

更多信息:

*   埃里克·埃利奥特的《嵌套 Ternaries 很棒》