# JavaScript 扩展运算符

> 原文：<https://dev.to/ericatallah/javascript-spread-operator-2p34>

什么是 JavaScript spread 运算符？你如何使用它？为什么要用看起来这么可笑的东西？这篇文章有望回答这些问题。

简单地说，扩展操作符是一种“扩展”数组或对象内容的方法。我知道，这本身没有意义，所以让我们开始吧。

让我们举一个简单的例子，从两个简单的数组开始:

```
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6]; 
```

现在，假设您想将它们合并成一个更大的数组？你可以这样做，虽然有点乏味:

```
let arr3 = [arr1[0], arr1[1], arr1[2], arr2[0], arr2[1], arr2[2]];
console.log(arr3); // [1, 2, 3, 4, 5, 6] 
```

..或者，如果你有一些大的数组，你试图合并，并蛮力迫使它不是一个选项(最有可能的情况)，你可以写一个函数，为你做“传播”:

```
let combineArrays = (arr1, arr2) => {
  let arr3 = [];
  arr1.forEach(item => {
    arr3.push(item);
  });
  arr2.forEach(item => {
    arr3.push(item);
  });

  return arr3;
};

console.log(combineArrays(arr1, arr2)); // [1, 2, 3, 4, 5, 6] 
```

如你所见，我们只是将两个数组合并成一个大数组，包含两个小数组的所有内容。简单，但是乏味。谢天谢地，有了 spread 运算符，一些全新的 JavaScript 让这一切变得简单多了！

让我们看看如何用一行代码完成上面的例子:

```
let arr3 = [...arr1, ...arr2];
console.log(arr3); // [1, 2, 3, 4, 5, 6] 
```

从上面的代码中我们可以看到，spread 操作符为我们处理了繁重的工作。

您也可以对对象做类似的事情:

```
let obj1 = {
  a: 'alpha',
  b: 'bravo'
};

let obj2 = {
  c: 'charlie',
  d: 'delta'
};

let obj3 = {
  ...obj1,
  ...obj2
};

console.log(obj3); // Contains all the items from obj1 and obj2 
```

你为什么要用这个？当你需要快速组合数组或对象时。

spread 运算符对于更新数组中的特定项也很有用。例如，我们有一个包含四个对象的数组，我们需要用一个不同的对象替换数组中的第三个对象:

```
let objectArray = [
  { a: 'alpha', b: 'bravo' },
  { c: 'charlie', d: 'delta' },
  { e: 'echo', f: 'foxtrot' },
  { g: 'golf', h: 'hotel' }
];

// replace item 3 (at index 2) with a new object:
objectArray = [
  ...objectArray.slice(0, 2),
  { n: 'new', o: 'object' },
  ...objectArray.slice(3)
];

console.log(objectArray); // We replaced "echo" and "foxtrot" with "new" and "object" 
```

注意，我们使用数组对象的“slice”方法来完成这个任务。Slice 只是返回由传递给它的整数定义的现有数组的一部分。

现在让我们更进一步。假设您有一个必须维护状态的应用程序。这种状态全部包含在一个对象数组中，您必须继续更新这个数组以反映这种变化的状态。因此，我们将创建一个函数，它接受一个对象数组(应用程序的当前状态)、一个新对象(应用程序的新状态)和一个索引(状态改变的位置):

```
let objectArray = [
  { a: 'alpha', b: 'bravo' },
  { c: 'charlie', d: 'delta' },
  { e: 'echo', f: 'foxtrot' },
  { g: 'golf', h: 'hotel' },
  { i: 'india', j: 'juliet' },
  { k: 'kilo', l: 'lima' },
  { m: 'mike', n: 'november' }
];

let updateState = (arr, newObject, i) => {
  arr = [
    ...arr.slice(0, i),
    newObject,
    ...arr.slice(i + 1)
  ];

  return arr;
};

let newState = updateState(objectArray, { u: 'updated', s: 'state' }, 2);

console.log(newState); // replaces "echo" and "foxtrot" with "updated" and "state" 
```

我希望这有所帮助，至少有一定的意义。点击查看更多[前端 web 开发教程。](https://onclickwebdesign.com/category/tutorials/)