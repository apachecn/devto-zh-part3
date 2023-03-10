# 清洗代码:避免循环

> 原文：<https://dev.to/sapegin/washing-your-code-avoid-loops-5gm>

[![](img/5cfa32af222d24fd3089f5913d500c74.png)](https://leanpub.com/washingcode/)

你正在阅读的是我即将出版的关于干净代码的书《清洗你的代码:写一次，读七次》的节选[在 Leanpub](https://leanpub.com/washingcode/) 上预订，或者[在线阅读草稿](https://github.com/sapegin/washingcode-book/blob/master/manuscript/book.md)。

传统的循环，像`for`或`while`，对于普通任务来说太低级了。它们冗长且容易出现[一个接一个的错误](https://en.wikipedia.org/wiki/Off-by-one_error)。你必须自己管理索引变量，我总是用`lenght`打错字。它们没有任何特定的语义值，除了你可能不止一次地做一些操作。

## 用数组方法替换循环

现代语言有更好的方式来表达迭代运算。 [JavaScript 有许多有用的方法](http://exploringjs.com/impatient-js/ch_arrays.html#methods-iteration-and-transformation-.find-.map-.filter-etc)来转换和迭代数组，比如`.map()`或`.find()`。

例如，让我们用一个`for`循环将一个字符串数组转换为`kebab-case`:

```
const names = ['Bilbo Baggins', 'Gandalf', 'Gollum'];
for (let i = 0; i < names.length; i++) {
  names[i] = _.kebabCase(names[i]);
} 
```

现在用`.map()`的方法:

```
const names = ['Bilbo Baggins', 'Gandalf', 'Gollum'];
const kebabNames = names.map(name => _.kebabCase(name)); 
```

如果我们的处理函数只接受一个参数，我们可以进一步缩短，Lodash 的 [kebabCase 接受:](https://lodash.com/docs#kebabCase) 

```
const names = ['Bilbo Baggins', 'Gandalf', 'Gollum'];
const kebabNames = names.map(_.kebabCase); 
```

但是这可能比扩展版本可读性差一点，因为我们看不到我们到底要传递给函数什么。与旧的匿名函数语法相比，ECMAScript 6 的箭头函数缩短了回调时间，减少了混乱:

```
const names = ['Bilbo Baggins', 'Gandalf', 'Gollum'];
const kebabNames = names.map(function(name) {
  return _.kebabCase(name);
}); 
```

或者让我们用一个`for`循环:
找到数组中的一个元素

```
const names = ['Bilbo Baggins', 'Gandalf', 'Gollum'];
let foundName;
for (let i = 0; i < names.length; i++) {
  if (names[i].startsWith('B')) {
    foundName = names[i];
    break;
  }
} 
```

现在用`.find()`的方法:

```
const names = ['Bilbo Baggins', 'Gandalf', 'Gollum'];
const foundName = names.find(name => name.startsWith('B')); 
```

在这两种情况下，我更喜欢使用数组方法的版本，而不是使用循环。它们更短，我们不会在迭代机制上浪费一半的代码。

## 隐含数组方法的语义

数组方法不仅更短、可读性更好；每种方法都有自己清晰的语义:

*   表示我们正在将一个数组转换成另一个元素个数相同的数组；
*   `.find()`说我们正在*寻找*一个数组中的单个元素；
*   `.some()`表示我们正在测试某些数组元素的条件是否为真；
*   `.every()`表示我们正在测试每个数组元素的条件是否为真。

传统的循环不能帮助理解代码在做什么，除非你读完整个代码。

我们将“什么”(我们的数据)与“如何”(如何循环)分开。更重要的是，使用数组方法，我们只需要担心我们的数据，这些数据是作为回调函数传入的。

当您对所有简单的情况使用数组方法时，传统的循环会向代码阅读器发出信号，表明有不寻常的事情正在发生。这很好:你可以保留大脑资源，以便更好地理解不寻常的、更复杂的案例。

同样，当更专业的数组方法可以工作时，不要使用通用的数组方法，如`.map()`或`.forEach()`，当`.map()`可以工作时，不要使用`.forEach()`:

```
const names = ['Bilbo Baggins', 'Gandalf', 'Gollum'];
const kebabNames = [];
names.forEach(name => {
  kebabNames.push(_.kebabCase(name));
}); 
```

这是`.map()`的一个更隐晦、语义更少的实现，所以最好像我们上面做的那样直接使用`.map()`:

```
const names = ['Bilbo Baggins', 'Gandalf', 'Gollum'];
const kebabNames = names.map(name => _.kebabCase(name)); 
```

这个版本更容易阅读，因为我们知道`.map()`方法通过保持相同数量的项来转换数组。与`.forEach()`不同，它不需要定制实现，也不改变输出数组。此外，回调函数现在是纯函数:它不访问父函数中的任何变量，只访问函数参数。

## 处理副作用

副作用使代码更难理解，因为您不能再将函数视为黑盒:具有副作用的函数不只是将输入转换为输出，还会以不可预测的方式影响环境。有副作用的函数也很难测试，因为您需要在每次测试前重新创建环境，并在测试后进行验证。

上一节提到的所有数组方法，除了`.forEach()`，都暗示它们没有副作用，只使用返回值。在这些方法中引入任何副作用都会使代码容易被误读，因为读者不会期望看到副作用。

`.forEach()`不返回任何值，当你真正需要副作用时，这是处理副作用的正确选择:

```
errors.forEach(error => {
  console.error(error);
}); 
```

`for of`循环更好:

*   它没有本章开头提到的常规循环的任何问题；
*   我们可以避免重分配和突变，因为我们没有返回值；
*   它对所有数组元素都有清晰的迭代语义，因为我们不能像在常规的`for`循环中那样控制迭代次数。(嗯，差不多，我们可以用`break`中止循环。)

让我们使用`for of`循环:
重写我们的例子

```
for (const error of errors) {
  console.error(error);
} 
```

## 有时候循环并没有那么糟糕

数组方法并不总是比循环好。例如，`.reduce()`方法通常会使代码比常规循环可读性差。

我们来看看这段代码:

```
const tableData = [];
if (props.item && props.item.details) {
  for (const client of props.item.details.clients) {
    for (const config of client.errorConfigurations) {
      tableData.push({
        errorMessage: config.error.message,
        errorLevel: config.error.level,
        usedIn: client.client.name
      });
    }
  }
} 
```

我的第一反应是用`.reduce()`到*重写它，避免循环* :

```
const tableData =
  props.item &&
  props.item.details &&
  props.item.details.clients.reduce(
    (acc, client) => [
      ...acc,
      ...client.errorConfigurations.reduce(
        (inner, config) => [
          ...inner,
          {
            errorMessage: config.error.message,
            errorLevel: config.error.level,
            usedIn: client.client.name
          }
        ],
        []
      )
    ],
    []
  ); 
```

但是真的可读性更强吗？

喝了一杯咖啡，和一位同事聊了一会儿，我得到了一个更简洁的代码:

```
const tableData =
  props.item &&
  props.item.details &&
  props.item.details.clients.reduce((acc, client) =>
    acc.concat(
      ...client.errorConfigurations.map(config => ({
        errorMessage: config.error.message,
        errorLevel: config.error.level,
        usedIn: client.client.name
      }))
    ),
    []
  ); 
```

我想我仍然更喜欢双`for`版本，但是如果我必须审查这样的代码，我会对两个版本都满意，原始版本和第二次重写版本。

*(虽然`tableData`确实是个很烂的变量名。)*

## 遍历对象

JavaScript 中有很多方法可以迭代对象。我同样不喜欢他们，所以很难选择最好的一个。不幸的是，没有针对对象的`.map()`,尽管 Lodash 有三个对象迭代方法，所以如果您已经在项目中使用 Lodash，这是一个不错的选择。

```
const allNames = {
  hobbits: ['Bilbo', 'Frodo'],
  dwarfs: ['Fili', 'Kili']
};
const kebabNames = _.mapValues(allNames, names =>
  names.map(name => _.kebabCase(name))
); 
```

如果不需要结果作为宾语，像上面的例子，`Object.keys()`、`Object.values()`、`Object.entries()`也不错:

```
const allNames = {
  hobbits: ['Bilbo', 'Frodo'],
  dwarfs: ['Fili', 'Kili']
};
Object.keys(allNames).forEach(race =>
  console.log(race, '->', allNames[race])
); 
```

或者:

```
const allNames = {
  hobbits: ['Bilbo', 'Frodo'],
  dwarfs: ['Fili', 'Kili']
};
Object.entries(allNames).forEach(([race, value]) =>
  console.log(race, '->', names)
); 
```

我对它们没有强烈的偏好。`Object.entries()`有更详细的语法，但是如果你不止一次地使用这个值(上面例子中的`names`，代码会比`Object.keys()`更干净，在`Object.entries()`中，你必须每次都写`allNames[race]`或者在回调函数的开始将这个值缓存到一个变量中。

如果我就此打住，那我就是在骗你。大多数关于对象迭代的文章都有使用`console.log()`的例子，但是实际上你经常想要将一个对象转换成另一个数据结构，就像上面使用`_.mapValues()`的例子一样。这就是事情变得越来越糟糕的地方。

让我们用`.reduce()` :
重写我们的例子

```
const kebabNames = Object.entries(allNames).reduce(
  (newNames, [race, names]) => {
    newNames[race] = names.map(name => _.kebabCase(name));
    return newNames;
  },
  {}
); 
```

带`.forEach()` :

```
const allNames = {
  hobbits: ['Bilbo', 'Frodo'],
  dwarfs: ['Fili', 'Kili']
};
const kebabNames = {};
Object.entries(allNames).forEach(([race, names]) => {
  kebabNames[race] = names.map(name => name.toLowerCase());
}); 
```

并带有循环:

```
const kebabNames = {};
for (let [race, names] of Object.entries(allNames)) {
  kebabNames[race] = names.map(name => name.toLowerCase());
} 
```

同样,`.reduce()`是可读性最差的选项。

在后面的章节中，我将敦促你不仅要避免循环，还要避免重新分配变量和突变。像循环一样，它们*经常*导致糟糕的代码可读性，但是*有时*它们是最好的选择。

## 但是数组方法不是很慢吗？

你可能认为使用函数比循环慢，事实很可能如此。但实际上，除非您正在处理数百万个项目，否则这并不重要。

现代 JavaScript 引擎非常快，并且针对流行的代码模式进行了优化。以前我们常常这样写循环，因为每次迭代检查数组长度太慢:

```
var names = ['Bilbo Baggins', 'Gandalf', 'Gollum'];
for (var i = 0, namesLength = names.length; i < namesLength; i++) {
  names[i] = _.kebabCase(names[i]);
} 
```

已经不慢了。还有其他一些例子，引擎优化了更简单的代码模式，使得手动优化变得没有必要。在任何情况下，您都应该测量性能，以了解要优化什么，以及您的更改是否真的能让代码在所有重要的浏览器和环境中运行得更快。

此外，`.every()`、`.some()`、`.find()`和`.findIndex()`将会短路，这意味着它们不会迭代不必要的数组元素。

* * *

开始思考:

*   用数组方法替换循环，比如`.map()`或`.filter()`。
*   避免函数中的副作用。

* * *

*如果你有任何反馈，[发微博给我](https://twitter.com/iamsapegin)，[在 GitHub 上开问题](https://github.com/sapegin/washingcode-book/issues)，或者发邮件给我 [artem@sapegin.ru](//mailto:artem@sapegin.ru) 。[在 Leanpub](https://leanpub.com/washingcode/) 上预订书籍或者[在线阅读草稿](https://github.com/sapegin/washingcode-book/blob/master/manuscript/book.md)。*