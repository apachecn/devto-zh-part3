# JS 基础知识:Array#map

> 原文：<https://dev.to/sergiodxa/conceptos-bsicos-de-js-arraymap-2hcb>

https://sdx.im/articles/js-basics/array-prototype-mapT3

*Array#map* 或`Array.prototype.map`是 JavaScript 中所有数组都有的方法。这种方法使我们能够将*映射到*数组以获得一个新数组，用更普通的话来说，这意味着我们将遍历数组并将它的元素转换为其他元素。让我们看看一个例子:

```
const numbers = [1,2,3];
const doubles = numbers.map(number => number * 2);
console.log(doubles); // [2,4,6] 
```

Enter fullscreen mode Exit fullscreen mode

然后，Array#map 所做的就是调用我们作为参数传递的函数(数组中的每个元素一次)。

> *注意:*:由于 Array#map 返回一个新数组，因此在遍历数组时使用它手动修改初始数组或执行某些其他操作是不良做法，因此最好使用 Array#forEach。

如果我们想做 Array#map 一样的事情，我们就得做类似这样的事情:

```
const numbers = [1,2,3];
const doubles = [];

for (const number of numbers) {
  doubles.push(number * 2);
}

console.log(doubles); // [2,4,6] 
```

Enter fullscreen mode Exit fullscreen mode

我们需要创建一个新的空数组，遍历第一个数组，然后将每个值乘以 2 来创建数组#push。

## 辛塔什

函数的完整语法如下:

```
const result = initial.map(function callback(currentValue, index, array) {
  // Devuelve un nuevo elemento
}, thisArg); 
```

Enter fullscreen mode Exit fullscreen mode

Array#map 从回调中接收两个参数，一个函数`callback`和一个值(任何值)，如果使用*箭头函数*，则该值不起作用。反过来，回调接收三个参数，大多数情况下只使用第一个参数，有时仅使用第二个参数，第一个参数是我们在迭代过程中遇到的当前值，第二个参数是我们数组中的索引，第三个参数是我们正在迭代的同一数组。

最后，该函数返回一个新数组，其结果是对初始数组中的每个元素运行了一次`callback`。

## 实现阵列#映射一个 mano

现在，让我们看一下如何创建我们自己的#map 阵列，以便更好地了解它的工作原理。我们将它作为一个名为`array`的函数来实现，其第一个值将是`array`进行映射，然后得到`callback`和`this`的值。

```
function map(array, callback, thisArg) {
  const result = [];
  const boundCallback = thisArg ? callback.bind(thisArg) : callback;
  for (let index = 0; index < array.length; index++) {
    result.push(boundCallback(array[index], index, array));
  }
  return result;
}

const numbers = [1,2,3];
const doubles = map(numbers, number => number * 2);
console.log(doubles); // [2,4,6] 
```

Enter fullscreen mode Exit fullscreen mode

是我们做的吗？我们的函数接收我们前面说过的三个参数，然后创建一个名为“`result`”的空数组和一个名为“`boundCallback`”的常量，如果定义了“`thisArg`”，则等于使函数# bind the“`callback`”的值为“`thisArg`”，否则等于“[t]”然后，我们迭代初始数组并使“`result.push`”返回的值变为“`callback`”并传递数组、索引和整个数组的当前值。最后，我们将返回结果。

## Casos de uso

Array#map 在 JavaScript 中使用很多，尤其是在以功能方式工作时。用于 React.js 中，将数据列表转换为 React 元素，例如:

```
function List({ list }) {
  return (
    <ul>
      {list.map(item => <Item key={item.id} {...item} />)}
    </ul>
  );
}

ReactDOM.render(<List list={[{ id: 1 }, { id: 2 }]} />, target); 
```

Enter fullscreen mode Exit fullscreen mode

本组件`List`使用 Array#map 将阵列`list`中的每个`item`转换为组件`Item`返回的元素，并将每个`item`的所有信息传递给该组件。

* * *

React 之外还可以用于处理 DOM 项目列表。

```
const $selectList = document.querySelectorAll("select");
const values = Array.from($selectList).map($select => $select.value); 
```

Enter fullscreen mode Exit fullscreen mode

此示例所做的是，使用“`Array.from`”将节点列表对象返回到页面中的所有“`values`”结果转换为数组，然后使用 Array#map 获取每个“`<select>`”的值列表(“T2”)。

## 结语

此方法非常有用，并且在使用 JavaScript 数组时每天都使用。如果您以前没有使用过它，我建议您尝试几个使用示例以熟悉它，当您使用它时，您会希望始终使用它，尤其是结合 Array#filter 和 Array#reduce 时。