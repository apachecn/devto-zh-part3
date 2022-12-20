# JavaScript 数组助手方法

> 原文：<https://dev.to/epicosity/javascript-array-helper-methods-40mp>

JavaScript 提供了大量的数组方法来帮助操作数据。下面我将介绍数组助手方法，以及为什么要在传统的 for 循环中使用它们。

### forEach

forEach helper 函数遍历数组中的每一项，并对每一项运行一次提供的回调函数。forEach 本质上取代了 for 循环。让我们看看如何使用 forEach 数组助手。

```
// using a for loop
const numbers = [1, 2, 3, 4, 5];

for (let i = 0; i < numbers.length; i++){
  console.log(numbers[i])
}

// Output
// 1
// 2
// 3
// 4
// 5 
```

让我们用一个经典的函数声明来重构它。

```
numbers.forEach(function(number){
  console.log(number);
});

// 1
// 2
// 3
// 4
// 5 
```

上面发生的是我们使用 forEach 来迭代数字数组。每次 forEach 从 numbers 数组中读取一个值时，它都会对当前值运行回调函数。回调函数将对当前值运行 console.log()。它看起来像这样。

```
// Iteration 1: console.log(number[0]) => 1
// Iteration 2: console.log(number[1]) => 2
// Iteration 3: console.log(number[2]) => 3
// Iteration 4: console.log(number[3]) => 4
// Iteration 5: console.log(number[4]) => 5 
```

和 for 循环做的完全一样，只不过代码要少得多。

快速笔记。我和许多其他人更喜欢使用带有箭头函数的数组助手，本文中的以下示例将使用带有数组助手的 ES6 箭头函数语法。如果你想重温一下箭头函数，请点击这里查看我的博客文章。

```
// using forEach with arrow
numbers.forEach( number => console.log(number)); 
```

使用 forEach，我们在上面的 console.log()中获取数字数组中的每个值。正如您所看到的，这个函数要短得多，并且执行相同的操作。

记住 forEach 回调还可以接受第二个参数 index。这将跟踪数字的当前索引。

```
numbers.forEach((number, index) => console.log(`Number: ${number} is at index: ${index}`));

// Number: 1 is at index: 0
// Number: 2 is at index: 1
// Number: 3 is at index: 2
// Number: 4 is at index: 3
// Number: 5 is at index: 4 
```

您可能想知道上面 console.log 中的语法是什么。我使用模板文字，这是 ES6 中另一个很棒的特性。如果你不知道它们是什么，看看这篇关于这个话题的帖子[这里](https://dev.to/epicosity/es6-template-strings-1c3p)

### 地图

map 助手是最有用的数组助手之一。像 forEach 一样，map helper 遍历一个数组，并在遍历该数组时对每个元素运行回调函数。Map 的不同之处在于它将返回一个新数组，其中每个元素的值都是提供给 map helper 的回调函数的返回值。当您希望在不改变原始数据集的情况下执行数据操作时，可以使用 Map。

下面的 map helper 返回一个新数组，其中包含 numbers 数组中每个值的平方。

```
const numbers = [1, 2, 3, 4, 5];

// using a for loop
const squared = [];

for (let i = 0; i < numbers.length; i++){
  squared.push(numbers[i] * numbers[i])
}

console.log(squared) // [1, 4, 9, 16, 25]

// using map
const squared = numbers.map(number => number * number);
console.log(squared)// [1, 4, 9, 16, 25] 
```

### 滤镜

filter helper 遍历数组并返回一个新数组，该数组将包含通过回调函数传递时返回 true 的值。过滤器助手中的回调函数可以被认为是一个测试函数。

让我们使用 filter 助手从 numbers 数组中返回一个包含所有偶数值的数组。

```
const numbers = [1, 2, 3, 4, 5, 13, 14, 21, 20];

// using a for loop
const filtered = [];

for (let i = 0; i < numbers.length; i++){
  if (numbers[i] % 2 === 0) {
    filtered.push(numbers[i])
  }
}

console.log(filtered); // [2, 4, 14, 20]

// using filter 
const filtered = numbers.filter( number => {
  if (number % 2 === 0){
    return true;
  }
});

console.log(filtered); // [2, 4, 14, 20] 
```

### 找到

find helper 返回数组中第一个元素的值，以通过所提供的回调函数中的测试。下面，我们将使用 find 来获取 numbers 数组中第一个大于 10 的值。

```
const numbers = [1, 2, 3, 4, 5, 10, 13, 14];

// using a for loop
let answer;

for (let i = 0; i < numbers.length; i++) {
  if (numbers[i] > 10){
    answer = numbers[i];
    break;
  }
}

console.log(answer); // 13

// using find
const answer = numbers.find( number => number > 10);

console.log(answer); //13 
```

### 有的

如果数组中至少有一个元素通过了回调函数中的测试，some array helper 将返回 true。

```
const numbers = [1, 3, 4, 5];

// using a for loop
let evenNumber = false;

for (let i = 0; i < numbers.length; i++){
  if(numbers[i] % 2 === 0) {
    evenNumber= true;
    break;
  }
}

console.log(evenNumber); // true

// using some
const evenNumber = numbers.some( number => {
  if(number % 2 === 0) {
    return true;
  }
});

console.log(evenNumber) // true 
```

### 每

只有当数组中的所有元素都通过测试函数时，everyarray helper 才会返回 true。我们将使用这个函数来检查数字数组中的所有值是否都小于 10。

```
const numbers = [1, 2, 3, 4, 5];

let isLessThanTen = true;

for (let i = 0; i < numbers.length; i++){
  if(numbers[i] >= 10) {
    isLessThanTen = false;
    break;
  }
}

console.log(isLessThanTen); // true

let lessThanTen = number => number < 10;

// every helper
let isLessthan10 = numbers.every(lessThanTen); 

console.log(isLessthan10); // true 
```

### 减少

为了简化 reduce 函数，可以使用 reduce 助手将一组值转换成一个值。有人会说 reduce 助手可以用来获取一组数据的本质。我们将使用 reduce 对 numbers 数组中的所有值求和。

```
const numbers = [1, 2, 3, 4, 5];

// using a for loop
let sum = 0;

for (let i = 0; i < numbers.length; i++){
  sum += numbers[i];
}

console.log(sum) // 15

// reduce helper
numbers.reduce((sum, number) => sum + number, 0); // 15 
```

因此，reduce 助手在每次迭代中执行回调函数，并在最后产生一个结果。在上面的例子中，这个值是 sum。

reduce helper 方法可以接受 5 个参数:

1.  蓄电池
2.  现值
3.  currentIndex
4.  排列
5.  初始值

```
array.reduce(function(accumlator, currentValue, currentIndex, array), initialValue) 
```

累加器和 currentValue 是必需的，而其他三个参数是可选的。在每次迭代中，reduce 助手首先检查是否有初始值被传递到函数中。如果已经传入了初始值，那么累加器的值被设置为等于初始值。如果没有传入初始值，那么累加器将被设置为所提供数组中元素的值。

在上面的代码中，我们使用累加器、currentValue 和 initialValuearguments 对 numbers 数组中的值求和。为了更好地理解 reduce 的工作原理，让我们遍历一下每个迭代。

如果初始值参数已经被传入，那么函数将设置累加器和等于初始值。我们传入一个初始值，所以在第一次迭代时，总和将被设置为 0。currentIndex 或 number 被设置为数组中的下一个值。在 reducehelper 函数的开头，它将是 1 或 numbers 数组中的第一个值。

我们将向 reduce 函数添加一个 console.log 来显示每次迭代的 sum 值。

```
const numbers = [1, 2, 3, 4, 5]
numbers.reduce( (sum, number) => return sum + number, 0);

// Iteration 1: sum = 0, number = 1; return sum = 1;
// Iteration 2: sum = 1, number = 2; return sum = 3;
// Iteration 3: sum = 3, number = 3; return sum = 6;
// Iteration 4: sum = 6, number = 4; return sum = 10;
// Iteration 5: sum = 10, number = 5; return sum = 15; 
```

现在你知道了，你可以用 reduce 从一组值中产生一个值。然而，reduce 真的很强大，它能做的不仅仅是对值求和。以下面的例子为例，我们有一个包含人和宠物列表以及它们的一些属性的数组。

```
let occupants = [
  {name: 'Tony', age: 26, species: 'human'},
  {name: 'Katey', age: 26, species: 'human'},
  {name: 'Marley', age: 5, species: 'canine'},
  {name: 'Harlow', age: 2, species: 'feline'},
  {name: 'Diana', age: 1, species: 'feline'}
]; 
```

比方说，你需要一份你家住户的名单，以便房东跟踪。您可以在 occupants 数组上使用 reduce 来生成一个新数组，该数组以字符串形式包含每个居住者的姓名。

```
const occupantNames = occupants.reduce((acc, occupant) => {
  acc.push(occupant.name);

  return acc;
}, []);

console.log(occupantNames) // ["Tony", "Katey", "Marley", "Harlow", "Diana"] 
```

### 结论

JavaScript 数组助手方法可以帮助您在更短的时间内编写更干净、更易读的代码。下次考虑使用 for 循环时，请尝试这些助手方法。

* * *