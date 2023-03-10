# JavaScript 中更好的循环

> 原文：<https://dev.to/kartik2406/better-loops-in-javascript-2716>

[![Loops](img/cb3e0d8f937f502cfdc5f6d57f01498b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AyD7bRed--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cgygkj9geu9zxxel8n98.jpg)

在这篇文章中，我们将看看 for 循环的形式，数组方法，使用它们你可以使你的代码更简单，更可读。当您想要操作数组或对象中的数据时，这些循环/方法非常有用。

### 为 in 循环

有了这种循环，你不必手动迭代数组索引和对象键。

```
//iterating over an array
let arr = [1, 2, 3, 4, 5]
for (let index in arr) {
  console.log(arr[index])
}
// Output: 1,2,3,4,5

//iterating over object keys
let obj = { id: 1, msg: 'hello' }
for (let key in obj) {
  console.log(obj[key])
}
// Output: 1, hello 
```

### 为循环的

使用这个循环你可以得到特定索引的值。这个循环只对数组有效。

```
for (let val of arr) {
  console.log(val)
}
// Output: 1,2,3,4,5 
```

## 阵列方法

现在让我们来看看几个数组循环方法。当处理数组时，循环往往会变得太大，我们必须显式地将项推入另一个数组，等等。以下方法使处理这种情况变得更容易。以下方法将回调函数作为参数，该函数将对数组中的每个元素执行。我们来看几个例子。

### 遍历一个数组

为此，可以使用 [array.forEach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 方法。For-each 将回调作为参数，并对每个数组元素执行回调。

```
// display index and value
arr.forEach((value, index) => console.log(`Index = ${index} Value = ${value}`)) 
```

### 变换一个数组

要将现有数组转换成另一种形式，可以使用 [array.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 方法。map()方法将回调作为参数，并返回一个新数组。新数组中的元素将是回调函数返回的值。

假设你有一个对象数组，每个对象都有 id，name。您想要一个只包含 id 的数组。

#### 同 for 循环

```
let data = [
  { id: 1, name: 'Phone', type: 'electronic' },
  { id: 2, name: 'Laptop', type: 'electronic' },
  { id: 3, name: 'Shirt', type: 'clothing' },
]
let ids = []
for (let i = 0; i < data.length; i++) {
  ids.push(data[i].id)
} 
```

#### 同地图

```
let ids = data.map(function(val) {
  return val.id
}) 
```

#### 甚至更短更简单的带箭头功能

```
let ids = data.map(val => val.id) 
```

### 从数组中过滤元素

要从数组中过滤元素，您可以使用 [array.filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 方法。filter()方法期望一个回调，这个回调将对数组中的每个元素执行&返回一个包含被过滤项的新数组。如果回调函数为给定的元素返回 true，那么该元素将在过滤后的数组中。

#### 选择电子项目

```
let electronics = data.filter(item => item.type == 'electronic') 
```

### 搜索数组中的元素

如果你想在数组中搜索一个元素，你可以使用 [array.find()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find) 方法。像这里讨论的所有其他方法一样，这个方法也需要回调。回调函数应该返回 true 或 false。回调返回 true 的第一个值将是此方法的输出。如果没有匹配，函数将返回 undefined。

#### 搜索姓名“电话”

```
data.find(val => val.name == 'Phone') 
```

### 从数组中获取单个值

要从数组中获取单个值，可以使用 [array.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 方法。reduce()方法接受一个回调函数，初始值作为参数。回调函数有一个累加器，currentValue 作为强制参数。Accumulator 包含从上次执行回调中获得的值，currentValue 是正在处理的数组元素。

#### 数组的和与积

```
let arr = [1, 2, 3, 4, 5]

//sum of array elements
arr.reduce((accumulator, currentValue) => (accumulator + currentValue), 0)
// where 0 is the initial value
// Output: 15

// product of array elements
arr.reduce((accumulator, currentValue) => (acc * currentValue), 1)
// Output: 120 
```

### 检查数组中至少一个元素的条件是否为真。

为此使用 [array.some()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some) 方法。如果数组中至少有一个元素的条件为真，此方法将返回 true，否则将返回 false。

```
let friends = [13, 15, 16, 18] //ages of group of friends

// checking if at least one of them is 18 or above
friends.some(val => val >= 18) 
```

### 检查数组中所有元素的条件是否为真

为此使用 [array.every()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every) 方法。如果数组中所有元素的条件都为真，此方法将返回 true，否则将返回 false。

```
let giftPrices = [300, 350, 399, 400]
let budgetPerGift = 450

let checkBudget = price => price <= budgetPerGift

giftPrices.every(checkBudget) // true

budgetPerGift = 300

giftPrices.every(checkBudget) // false 
```

### 事情要照顾

*   数组方法比普通的循环的[稍慢，但是它们提供了很多优势，并且它们的性能会随着 JS 引擎的变化而提高。](https://hackernoon.com/javascript-performance-test-for-vs-for-each-vs-map-reduce-filter-find-32c1113f19d7)
*   我们上面讨论的所有方法(除了 some()、find())都在整个数组上执行。如果你不想这样做，那么这些方法对你来说是没有用的。不能用 break 来停止回调。

今天到此为止。这些方法有很大的潜力，仔细阅读它们在 MDN 上的文档，尝试这些方法。

如果你喜欢这篇文章，请分享它:)。