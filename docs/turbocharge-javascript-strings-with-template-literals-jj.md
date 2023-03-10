# 用模板文字增强 JavaScript 字符串

> 原文：<https://dev.to/georgehanson/turbocharge-javascript-strings-with-template-literals-jj>

任何长期编写 JavaScript 的人都会知道它已经突飞猛进了。ES6 已经发布了一段时间，但是我仍然看到很多开发者忘记或者没有意识到它们的存在。

今天我将展示我个人最喜欢的模板文字之一！

## 在 ES6 之前

在过去，当我们使用 jQuery 做任何事情时，字符串连接是相当有限的。如果我们想构造一个字符串，很快就会变得混乱。这里有一个例子。

```
var person = {
  name: 'George',
  age: 23,
  location: 'England'
}

var sentence = 'Hi, my name is ' + person.name + '. I am ' + person.age + ' years old and I live in ' + person.location 
```

虽然这种方法有效，但看起来很混乱。如果某个东西可以被整理并变得更具可读性，我个人认为这是值得的。

## [ES6 后的](#after-es6)

当 ES6 到来时，它带来了一系列功能。模板文字允许您轻松地构造这些字符串，而不会使连接看起来太混乱。

让我们举上面同样的例子。我们如何使用 ES6 模板文字做到这一点？嗯，很简单。下面是我们如何做完全相同的事情，但在一个更可读的方式。

```
let person = {
  name: 'George',
  age: 23,
  location: 'England'
};

let sentence = `Hi, my name is ${person.name}. I am ${person.age} years old and I live in ${person.location}.` 
```

如你所见，有了模板文字，我们不再需要使用引号或`+`操作符来连接字符串。相反，我们可以只使用`${`和`}`。我觉得这是一个很好的进步。

为了使用模板文字，你会注意到我们使用反勾号而不是引号。这也很有用，因为这意味着我们不再需要对字符串中的引号进行转义。例如，如果不使用模板文字，我们必须这样做:

```
let greeting = 'Hello! I\'m pleased to meet you!' 
```

但是对于模板文字，我们不使用引号来表示字符串。所以我们可以直接包含它们，而不必对它们进行转义。

```
let greeting = `Hello! I'm pleased to meet you!` 
```

我要说的最后一点是，你也可以在模板中计算表达式。例如，如果我想加倍这个人的年龄，我可以这样写:

```
let sentence = `Hi, my name is ${person.name}. I am ${person.age*2} years old and I live in ${person.location}.` 
```

我希望这对你有用，我发现自己一直在使用模板文字。

*本文原贴于此:[https://www . Georg Hanson . co . uk/code-tips/es6-JavaScript-tips-template-literals](https://www.georgehanson.co.uk/code-tips/es6-javascript-tips-template-literals)*