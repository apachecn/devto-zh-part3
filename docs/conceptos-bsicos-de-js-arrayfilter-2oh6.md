# JS 基础知识:Array#filter

> 原文：<https://dev.to/sergiodxa/conceptos-bsicos-de-js-arrayfilter-2oh6>

https://sdx.im/articles/js-basics/array-prototype-filterT3

*Array#filter* 或`Array.prototype.filter`是 JavaScript 中所有数组都有的方法。这种方法用于筛选数组并获取新数组，也就是说，我们将遍历数组并对其每个元素应用一个条件，然后只保留那些通过的元素。让我们看看一个例子:

```
const numbers = [1,2,3];
const evens = numbers.filter(number => number % 2 === 0); // es par
console.log(evens); // [2] 
```

Enter fullscreen mode Exit fullscreen mode

Array#filter 接着会针对阵列中的每个元素，呼叫我们当做引数传递的函数一次。

> *注意:*:由于 Array#filter 返回新阵列，因此在遍历阵列时使用它手动修改初始阵列或执行其他操作是不良做法，因此最好使用 Array#forEach。

如果我们想做 Array#filter 那样的事情，我们就必须做类似这样的事情:

```
const numbers = [1,2,3];
const evens = [];

for (const number of numbers) {
  if (number % 2 === 0) evens.push(number);
}

console.log(evens); // [2,4,6] 
```

Enter fullscreen mode Exit fullscreen mode

我们必须创建一个新的空数组，遍历第一个数组，然后对通过条件的每个值进行数组#push

## 辛塔什

函数的完整语法如下:

```
const result = initial.filter(function callback(currentValue, index, array) {
  // Devuelve true o false para saber si debe quedarse o no
}, thisArg); 
```

Enter fullscreen mode Exit fullscreen mode

Array#filer 接收两个参数，一个函数`callback`和一个值(任意值)，如果使用*箭头函数*，则该值不起作用。反过来，回调接收三个参数，大多数情况下只使用第一个参数，有时仅使用第二个参数，第一个参数是我们在迭代过程中遇到的当前值，第二个参数是我们数组中的索引，第三个参数是我们正在迭代的同一数组。

最后，该函数返回一个新数组，其结果是对初始数组中的每个元素运行了一次`callback`。

## Implementando 数组#filter a mano

现在，让我们看一下如何创建自己的#filter 阵列，以便更好地了解其工作原理。我们将它作为名为`array`的函数来实现，其第一个值将是`array`，然后得到`callback`和`this`的值。

```
function map(array, callback, thisArg) {
  const result = [];
  const boundCallback = thisArg ? callback.bind(thisArg) : callback;
  for (let index = 0; index < array.length; index++) {
    if (boundCallback(array[index], index, array)) result.push();
  }
  return result;
}

const numbers = [1,2,3];
const evens = filter(numbers, number => number % 2 === 0);
console.log(evens); // [2,4,6] 
```

Enter fullscreen mode Exit fullscreen mode

是我们做的吗？我们的函数接收我们前面说过的三个参数，然后创建一个名为“`result`”的空数组和一个名为“`boundCallback`”的常量，如果定义了“`thisArg`”，则等于将“`callback`的 Function#bind”设置为“`thisArg`”的值，否则等于“[t]然后，我们迭代初始数组并做`result.push`如果返回的值`callback`是`true`，则将数组的当前值、索引和整个数组传递给它。最后，我们将返回结果。

## Casos de uso

Array#filter 在 JavaScript 中使用很多，尤其是在以功能性方式工作时，以及结合使用其他方法(如[array # map](https://dev.to/essays/js-basics/array-prototype-filter))时。在 React.js 中用于过滤 React 元素中的数据列表，例如:

```
function List({ list, query }) {
  return (
    <ul>
      {list
        .filter(item => item.content.includes(query))
        .map(item => <Item key={item.id} {...item} />)
      }
    </ul>
  );
}

ReactDOM.render(
  <List list={[{ id: 1, content: "hola" }, { id: 2, content:  }]} query="hola" />,
  target
); 
```

Enter fullscreen mode Exit fullscreen mode

此组件`List`使用 Array#filter 根据每个元素的内容进行过滤，而 Array#map 则通过将每个`item`的所有信息传递给组件，将阵列筛选器`list`之后剩下的每个`item`转换为组件`Item`返回的元素。

* * *

React 之外还可以用于处理 DOM 项目列表。

```
const $selectList = document.querySelectorAll("select");
const values = Array
  .from($selectList)
  .map($select => $select.value)
  .filter(value => value !== "") 
```

Enter fullscreen mode Exit fullscreen mode

此示例所做的是获取页面上返回 NodeList 对象的所有“T0”，方法是使用“`Array.from`”将其转换为数组，然后使用 Array#map 获取每个“`<select>`”的值列表(“T2”)，最后使用 Array#filter 删除等于空字符串的值，该空字符串可以

## 结语

此方法非常有用，并且在使用 JavaScript 数组时每天都使用。如果您以前没有使用过它，我建议您尝试几个使用示例以熟悉它，当您使用它时，您会希望始终使用它，尤其是结合 Array#map 和 Array#reduce 时。