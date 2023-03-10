# 列表的基本 JavaScript 代码

> 原文：<https://dev.to/nestedsoftware/essential-javascript-code-for-lists-f1o>

edA-qa ( [@mortoray](https://dev.to/mortoray) )最近发表了几篇关于编码面试列表操作的好文章。[的第一篇文章](https://dev.to/mortoray/essential-code-for-lists-and-vectors-in-an-interview-5255)概述了需要练习的内容，[的第二篇文章](https://dev.to/mortoray/essential-python-3-code-for-lists-45f0)展示了 Python 中的代码示例。我认为用 JavaScript 展示这些例子也是有用的！

[![mortoray image](img/31b5b64babf1bea612e0e3e83d446353.png)](/mortoray) [## 列表的基本 Python 3 代码

### edA QA mort ora y Mar 7 ' 193min read

#python #programming #beginners #interview](/mortoray/essential-python-3-code-for-lists-45f0)

我试图尽可能接近 edA-qa 文章中的 Python 示例。然而，值得记住的是 Python 的标准库比 JavaScript 的丰富得多。对于这些列表操作练习，Python 的生成器表达式和丰富的迭代 api 特别有用。我已经尽可能地让事情简单明了，但是在一些地方我不得不写一些实用函数来弥补这一点。

我要感谢 edA-qa 对这篇文章的 JavaScript 版本进行了咨询，并帮助我澄清和简化了一些示例。不过，我会对任何错误或问题负责。

> 这些例子是使用现代 JavaScript 编写的，包括[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)、 [spread 和 rest](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 语法，以及[生成器函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)。

## 基础知识

```
// Create a list
let items = []

// Add element to the front or back
let newLength = items.unshift("abc")
let newLength = items.push("def")

// Pop element off front or back
let start = items.shift()
let end = items.pop()

// Forward iterate over elements
for (const item of items)
  console.log(item)

// Also works
items.forEach(item=>console.log(item))

// Get the length of a list
let length = items.length

// Test if empty. May need extra logic for special cases,
// such as null, undefined, or not iterable
if (items.length == 0)
  console.log("list is empty")

// Also works
console.log(items.length == 0 ? "list is empty" : "list is not empty") 
```

## 基于位置的操作

```
// Get item at location
let thirdItem = items[2]

// Insert an item at location
items.splice(3, 0, "abc")

// Remove an item from a location
let removedItems = items.splice(2, 1)

// Replace/Assign item at location
items[1] = "def" 
```

## 排序和搜索

```
// Find an item
let index = items.indexOf(item) // -1 if not found

// Find and remove an item
let index = items.indexOf(item)
if (index > -1)
  items.splice(index, 1)

// Find last matching item
let index = items.lastIndexOf(item) // -1 if not found

// Sort by natural order
// in-place sort
items.sort()

// Sort with custom comparator
let items = [['a', 10], ['b', 7], ['c', 13], ['d', 1]]
items.sort((a, b) => a[1]-b[1])
// produces [ [ 'd', 1 ], [ 'b', 7 ], [ 'a', 10 ], [ 'c', 13 ] ] 
```

## 分段操纵

```
// Split the list at arbitrary location
let left = items.slice(0, 4)
let right = items.slice(4, items.length)

// Multiple splits based on a match
let items = ['a', 'b', 'c', 'd', 'b', 'e']
let splits = groupBy(items, item=>item=='b').filter(group=>group.indexOf('b')==-1)
// produces [['a'], ['c','d'], ['e']]

// groupBy is not built in. Groups items by whether or not they 
// match the `isSeparator` function. The result is an array of arrays
const groupBy = (items, isSeparator) => {
  const groups = []

  let rest = items
  while (rest.length > 0) {
    const {group, leftover} = nextGroup(rest,  isSeparator)
    groups.push(group)
    rest = leftover
  }

  return groups
}

const nextGroup = (items, isSeparator) => {
  const foundIndex = items.findIndex(isSeparator(items[0]) != isSeparator(item))
  const endIndex = foundIndex > -1 ? foundIndex : items.length

  return {group: items.slice(0, endIndex), leftover: items.slice(endIndex)}
}

// Clear the list (in place)
items.length = 0

// Remove segment
// delete 2 items starting with the second item (index 1)
let removedItems = items.splice(1, 2)

// Concatenate lists
let combined = items.concat(moreItems)

// Insert list at location
items.splice(index, 0, ...moreItems) //not a good idea for large lists... watch the stack!

//Get a sublist
//sublist starting at position 1 up to, but excluding, position 3
let slice = items.slice(1, 3) 
```

## 更多迭代

```
// Backward
for (const item of items.reverse())
  console.log(item)

// Partial segment iteration
for (const item of items.slice(1, 4))
  console.log(item)

// Skipping elements
// step from element at index 1 to 6 (exclusive) by 2
items.slice(1,6).filter((item, index)=>index % 2 == 0).map(item=>console.log(item)) 
```

## 创作

```
// Create from a static list of items
let items = [ 'abc', 'def', 'ghi']

// Create a range of numbers
// a list of numbers from 10..20 (exclusive)
let numbers = [...Array(10).keys()].map(n=>n+10)

// Also works
let numbers = []
for (let i=10; i<20; i++)
  numbers.push(i) 
```

## 数据操作

```
// Mapping
let numbersTimes10 = numbers.map(n=>n*10)

// Filtering
let evenNumbers = numbers.filter(n=>n%2==0)

// Fold / Reduce
// If a default value is not supplied to reduce,
// then reducing an empty list will produce a TypeError at runtime
let total = numbers.reduce((acc,n)=>acc+n, 0)

// Joining string representations of items
let joined = items.reduce((left,right)=>left + '/' + right, '')

// Zip
let results = zip([[1,2,3], ['a','b','c'], ['x','y','z']]) 
// produces [[1,'a','x'],[2,'b','y'],['3','c','z']]

// zip is not built in
const zip = rows=>rows[0].map((val,colIndex)=>rows.map(row=>row[colIndex]))

// alternative version of zip (takes each array as separate parameter)
let results = zip([1,2,3], ['a','b','c'], ['x','y','z'])

const zip = (...rows)=>rows[0].map((val,colIndex)=>rows.map(row=>row[colIndex]))

// to alternate items into one list use reduce
zip([0,2,4], [1,3,5]).reduce((acc, item)=>acc.concat(item)) //[0,1,2,3,4,5] 
```

## 高级

```
// Swap elements at two locations
[items[3], items[5]] = [items[5], items[3]]

// Reserve capacity
// JavaScript arrays do not expose capacity. In fact, 
// they are not arrays in the usual sense, since items 
// are not stored contiguously in memory. 
// See https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays
// if you need something closer to the metal

// Replace content in a list in place
replace(mylist, anotherList)

const replace = (target, source) => {
  target.length = 0
  return target.splice(0,0,...source) // not a good idea for large lists... watch the stack!
}

// Compare two arrays
JSON.stringify(items1) === JSON.stringify(items2)  //simple-minded approach

// Search a sorted list
const foundIndex = findFirst(sortedNumbers, 4)
if (foundIndex !== -1)
  console.log(`Found at ${foundIndex}`)

const findFirst = (sortedItems, target) => {
  const index = bisectLeft(sortedItems, target)
  return index < sortedItems.length && sortedItems[index] === target ? index : -1
}

// bisectLeft is not built in. Performs a binary search 
// for the left-most matching item. If the item isn't found, 
// returns the index where it should go.
// Comparison only works properly if `valueOf` has been implemented.
const bisectLeft = (sortedItems, target) => {
  let start = 0
  let end = sortedItems.length

  while (start < end) {
    let mid = Math.trunc((start + end) / 2)
    if (sortedItems[mid] < target) 
      start = mid + 1
    else 
      end = mid
  }

  return start
}

// Multiple iterators at the same time
let itemsA = [1,2,3]
let itemsB = ['a','b','c','d','e','f']

let iterA = itemsA[Symbol.iterator]()
let iterB = itemsB[Symbol.iterator]()

while(true) {
  let a = iterA.next()
  let b = iterB.next()

  if (a.done || b.done)
    break

  console.log(a.value + ', ' + b.value)
} 
```

## 附录 A:迭代

下面的例子展示了如何使用[生成器函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)来迭代数组的一部分或以相反的顺序，而不必修改或复制原始数组。

```
// Backward
for (const item of reverseGenerator(items))
  console.log(item)

const reverseGenerator = function* (items) {
  for (let i = items.length-1; i>=0; i--)
    yield items[i]
}

// Partial segment iteration
for (const item of sliceGenerator(items, 1, 4))
  console.log(item)

// Skipping elements
// step from element at index 1 to 6 (exclusive) by 2
for (const item of sliceGenerator(items,1,6,2))
  console.log(item)

const sliceGenerator = function* (items, start=0, end=items.length, step=1) {
  for (let i=start; i<end; i+=step)
    yield items[i]
} 
```

## 附录 B:可变性

其中一些示例就地修改数组，而另一些示例返回包含所需更改的数组副本。以下操作会就地更改阵列:

*   [推动](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)、[弹出](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)、[未换档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift)、[换档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)、[拼接](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)、[排序](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)、[反转](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)
*   在索引处为[数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)分配新值，例如`items[index] = newValue`
*   将数组重置为空，例如`items.length = 0`
*   交换，例如`[items[3], items[5]] = [items[5],items[3]]`

这些操作不会修改原始数组:

*   [切片](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)，[拼接](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)(对原数组做浅拷贝)
*   [贴图](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)，[滤镜](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)，[减少](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

> `map`、`filter`和`reduce`理论上可以用来修改原始数组的内容，但这是一种不好的做法，应该避免。

## 谢谢！

如果你一直读到最后，谢谢你的阅读。我认为将这个备忘单扩展到越来越多的语言会很棒。如果您觉得这很有趣，我强烈建议您用自己选择的编程语言来尝试一下！