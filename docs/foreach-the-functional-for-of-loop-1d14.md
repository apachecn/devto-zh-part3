# JavaScript 数组:。forEach -循环的函数 for

> 原文：<https://dev.to/cedpoilly/foreach-the-functional-for-of-loop-1d14>

遇见`Array​.prototype​.for​Each()`。

它的目的是在数组的每一项上执行你提供的代码:本质上是一个**循环**。

下面是它的定义:

```
array.forEach(function callback(currentValue [, index [, array]]) {  
// code for this iteration 
}[, thisArg]); 
```

下面来解释一下。😉

# 它的参数

`forEach`最多接受两个参数:

*   `callback`函数，对数组的每一项执行
*   `thisArg`(可选)，改变回调函数中`this`的值

现在，更深入地看看每一个。👇

## 1)`callback`功能

对数组的每个元素调用的方法。它最多需要三个参数:

*   currentValue:数组的当前项，`🍎`在第一次迭代时
*   index(可选):当前项的索引，在第一次迭代时
*   array(可选):整个数组，所有迭代的`same`

```
const array = ["🍎", "🍌", "🍍"];

array.forEach(function(current, index, array) {  
console.log(current);  
console.log(index);  
console.log(array);  
console.log("\n");  
});

// LOGS 
// { current: '🍎' } 
// { index: 0 } 
// { array: [ '🍎', '🍌', '🍍' ] } 
// 
// { current: '🍌' } 
// { index: 1 } 
// { array: [ '🍎', '🍌', '🍍' ] } 
// 
// { current: '🍍' } 
// { index: 2 } 
// { array: [ '🍎', '🍌', '🍍' ] } 
```

### `index`参数

`index`参数是可选的。当逻辑依赖于项目在数组中的位置时，这很方便。

```
const fruitEmojis = ["🍎", "🍌", "🍍"];  
const fruitNames = ["apple", "banana", "pineapple"];

fruitEmojis.forEach(function logFruitName(currentFruitEmoji, index) {  
const fruitName = fruitNames[index];

console.log({ emoji: currentFruitEmoji, name: fruitName });  
});

// LOGS 
// { emoji: '🍎', name: 'apple' } 
// { emoji: '🍌', name: 'banana' } 
// { emoji: '🍍', name: 'pineapple' } 
```

### `array`参数

最后一个参数是`array`。它是回调方法执行开始时整个数组的值。
**:当你有一个泛型方法传递给需要访问数组的`forEach`时，这很有用。

方法是泛型的，你无法预先知道它将被调用的数组。这也意味着你不能依赖闭包，因为它是未知的。

因此，在这种情况下，`array`参数是您访问当前数组的唯一**选项。****

 **更多信息请参见来自 [redneb](https://stackoverflow.com/users/6776093/redneb) 的 Stackoverflow 响应[回复](https://stackoverflow.com/a/39528692)。

## 2)`thisArg`参数

覆盖`callback`函数中的`this`关键字值。

默认情况下`this`会引用窗口对象，它会被你传递给它的值覆盖。

```
const array = [1];

array.forEach(function basicCall(current) {  
console.log(this);  
});

// LOGS 
// ... the whole Window object actually (in the browser)

const thisArg = { context: "custom" };

array.forEach(function explicitThisArg(current) {  
console.log(this);  
}, thisArg);

// LOGS 
//`{context: "custom"}`, which is the custom `this` value passed 
```

# 我的用法`forEach`

当我想对某个对象或另一个数组应用副作用时，我通常会使用它。(我尽可能避免副作用。)

## 举例

在这种情况下，我们有一个表情列表和相应的名称列表。我们希望创建一个对象，其中的关键字是名称，值将包含表情符号。

这两个数组以相同的方式排序:在任何给定的索引处，两个数组中的项都对应。

```
const fruitEmojis = ["🍎", "🍌", "🍍"];  
const fruitNames = ["apple", "banana", "pineapple"];

let fruitMap = {};

fruitEmojis.forEach(function addKeyPairToFruitMap(currentFruitEmoji, index) {  
const key = fruitNames[index];

fruitMap[key] = currentFruitEmoji;  
});

console.log(fruitMap);

// LOGS 
// { apple: "🍎", banana: "🍌", pineapple: "🍍" } 
```

注意，`fruitMap`是在`fruitEmojis`上调用 forEach 之前创建的。我们在执行`addKeyPairToFruitMap`的过程中更新对象。

# 信息考虑

在对数组使用`forEach`方法之前，这里有一些值得了解的信息。

## 1)返回`undefined`，因此不可链接

`forEach`数组方法总是返回`undefined`，因此**不**可链接。这意味着在一个*调用链*中，它只能放在**端**。

```
const fruitEmojis = ["🍎", "🍌", "🍍"];

let fruitMap = {};

fruitEmojis  
.forEach(function addKeyPairToFruitMap(currentFruitEmoji) {  
return currentFruitEmoji;  
})  
.map(function logEmoji(emoji) {  
console.log("Calling `.map` will throw an error!");  
}  
);

// LOGS (console.error) 
// ... (omitted details) 
// .map(function logEmoji(emoji) { 
// ^ 
// TypeError: Cannot read property 'map' of undefined 
// ... (omitted details) 
```

## 2)回调函数可以修改原数组

我们可以在回调函数中添加/删除/更新数组中的项。

### 加法

添加**不影响**调用的项目:只处理最初出现的项目。

但是执行之后我们看到它受到了影响。

```
const fruitEmojis = ["🍎", "🍌", "🍍"];

let fruitMap = {};

fruitEmojis.forEach(function addKeyPairToFruitMap(currentFruitEmoji, index) {  
fruitEmojis.push(`test ${index}`);

console.log({index});  
});

console.log({fruitEmojis});

// LOGS

// `forEach`: 
// { index: 0 } 
// { index: 1 } 
// { index: 2 }

// logging the array: 
// { fruitEmojis: [ '🍎', '🍌', '🍍', 'test 0', 'test 1', 'test 2' ] } 
```

### 删除

删除**会影响通话的项目数量吗？如果删除了下一个计划物料，则不会对其进行处理。** 

```
let fruitEmojis = ["🍎", "🍌", "🍍"];

let fruitMap = {};

fruitEmojis.forEach(function addKeyPairToFruitMap(currentFruitEmoji, index) {  
fruitEmojis.shift();  
fruitEmojis.pop();  
fruitEmojis.splice(0, 1);

console.log({index});  
});

console.log({fruitEmojis});

// LOGS

// `forEach`: 
// { index: 0 }

// logging the array: 
// { fruitEmojis: [] } 
```

### 修改

修改**是否影响物品**本身**为调用，但不影响计数。如果我们修改下一个计划物料，则在处理该物料时，可以进行修改。

注意，第四项是由于这条语句，下面这条语句在最后一次执行时向数组中添加了一项:`fruitEmojis[index + 1] = "AAAAAARH!";`。** 

```
let fruitEmojis = ["🍎", "🍌", "🍍"];

let fruitMap = {};

fruitEmojis.forEach(function addKeyPairToFruitMap(currentFruitEmoji, index) {  
fruitEmojis[index + 1] = "AAAAAARH!";  

console.log({currentFruitEmoji, index});  
});

console.log({fruitEmojis});

// LOGS

// `forEach`: 
// { currentFruitEmoji: '🍎', index: 0 } 
// { currentFruitEmoji: 'AAAAAARH!', index: 1 } 
// { currentFruitEmoji: 'AAAAAARH!', index: 2 }

// the array 
// { fruitEmojis: [ '🍎', 'AAAAAARH!', 'AAAAAARH!', 'AAAAAARH!' ] } 
```

## 3)不能停止

调用 forEach 方法时，不能停止执行或“中断循环”。

如果你试图停止执行，你可能应该使用不同的数组方法(如 find、filter、reduce、some、includes)或使用一个 **for-loop** 来代替。

# 结论

希望这篇关于`forEach`法的文章给你带来了价值。🙂

这将是 JavaScript 数组系列的一部分，敬请关注下一期！🎉

直到那时，快乐编码！😎******