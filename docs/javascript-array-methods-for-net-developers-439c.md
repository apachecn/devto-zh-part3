# 的 JavaScript 数组方法。Net 开发人员

> 原文：<https://dev.to/kaos/javascript-array-methods-for-net-developers-439c>

# [T1】简介](#intro)

有一件事很多人。Net 开发者的最爱是`System.Linq`。Linq 是处理可枚举数时功能强大的扩展方法集合。
这个帖子会比较普通。Net 可枚举方法和 JavaScript 数组方法。

## [T3`.Any()`/`.some()`](#-raw-any-endraw-raw-some-endraw-)

检查**序列中是否至少有一个**元素满足条件。
返回类型:`Boolean`。
**用于空序列时，任何给定条件**的返回值将为`false`。

#### 例子

```
let arr = [1, 4, 5, 6];

arr.some(c => c > 6); // false
arr.some(c => c > 5); // true
arr.some();           // Uncaught TypeError: undefined is not a function
arr.some(_ => _);     // true
[].some(c => c > 5);  // false
[].some(_ => _);      // false 
```

```
var list = new List<int>() { 1, 4, 5, 6 };

list.Any(c => c > 6);  // false
list.Any(c => c > 5);  // true
list.Any();            // true
new List<int>().Any(); // false 
```

#### 延伸

您可能已经注意到，如果不传递回调函数，我们就不能使用`.some()`。在这种方法中，我们将`any()`函数添加到`Array`类型中，因此我们可以在没有类似参数的情况下使用它。网`.Any()`。

```
Array.prototype.any = function(callbackfn) {
    if(typeof callbackfn !== 'undefined') {
        return this.some(callbackfn);
    } else {
        return this.some(_ => _);
    }
}

[].any();              // false
[1].any();             // true
[1].any(c => c === 1); // true 
[1].any(c => c === 2); // false 
```

## [T3`.All()`/`.every()`](#-raw-all-endraw-raw-every-endraw-)

检查**序列中的所有**元素是否满足条件。
返回类型:`Boolean`。
**用于空序列时，任何给定条件**的返回值将为`true`。

#### 例子

```
let arr = [1, 4, 5, 6];

arr.every(c => c > 0); // true
arr.every(c => c > 5); // false
[].every(c => c > 5);  // true
[].every();            // Uncaught TypeError: undefined is not a function
[].every(_ => _);      // false 
```

```
var list = new List<int>() { 1, 4, 5, 6 };

list.All(c => c > 0);  // true
list.All(c => c > 5);  // false 
```

## [T3`.Select()`/`.map()`](#-raw-select-endraw-raw-map-endraw-)

对序列中的每个元素执行给定的方法，并返回新的序列。将序列中的所有元素转换成另一种表示/类型非常有用。

#### 例子

```
let arr = [1, 4, 5, 6];

arr.map(c => c * 2);  // [2, 8, 10, 12]
arr.map(String);      // ["1", "4", "5", "6"] 
```

```
var list = new List<int>() { 1, 4, 5, 6 };

list.Select(c => c * 2);         // [2, 8, 10, 12]
list.Select(c => c.ToString());  // ["1", "4", "5", "6"] 
```

## [T3`.Where()`/`.filter()`](#-raw-where-endraw-raw-filter-endraw-)

根据给定条件筛选序列，并返回筛选后的序列。如果没有找到元素，则返回一个空序列。

#### 例子

```
let arr = [1, 4, 5, 6];

arr.filter(c => c > 4);  // [5, 6] 
arr.filter(c => c > 7);  // [] 
```

```
var list = new List<int>() { 1, 4, 5, 6 };

list.Where(c => c > 4);  // [5, 6]
list.Where(c => c > 7);  // [] 
```

## [T3`.OrderBy()`/`.sort()`](#-raw-orderby-endraw-raw-sort-endraw-)

按升序对序列进行排序。
`.sort()`方法接受一个比较函数，这给了你很大的灵活性。在这个例子中，我们将保持事情简单，定义我们的比较函数类似于`.OrderBy()`的排序方式。[查看 mdn 文档，了解完整的解释。](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)

#### 例子

```
let arr = [
    {
        name: 'first',
        order: 2,
    },
    {
        name: 'second',
        order: 1,
    },
    {
        name: 'third',
        order: 3
    }    
];
arr.sort((a, b) => a.order > b.order ? 1 : -1).map(c => c.name);   // ["second", "first", "third"] 
```

```
 public class Item 
{
    public string Name { get; set; }
    public int Order{ get; set; }
};

var list = new List<Item>() 
{
    new Item() { Name = "First", Order = 2}, 
    new Item() { Name = "Second", Order = 1}, 
    new Item() { Name = "Third", Order = 3}, 
}
list.OrderBy(c => c.Order).Select(c => c.Name); // ["Second", "First", "Third"] 
```

## [T3`.FirstOrDefault()`/`.find()`](#-raw-firstordefault-endraw-raw-find-endraw-)

在序列中找到满足条件的第一个**元素。如果没有找到元素，`.find()`返回`undefined`，而`.FirstOrDefault()`返回`null`。也有其他口味的。Net，比如`.LastOrDefault()`、`.First()`和`.Last()`，它们在 JavaScript 中没有对应的版本。**

#### 例子

```
let arr = [1, 4, 5, 6];

arr.find(c => c > 4);  // 5 
arr.find(c => c > 7);  // undefined
arr.find(_ => _);      // 1 
```

```
var list = new List<int>() { 1, 4, 5, 6 };

list.FirstOrDefault(c => c > 4);  // 5
list.FirstOrDefault(c => c > 7);  // 0 (default(int) ≙ 0)
list.FirstOrDefault();            // 1 
```

## 快速比较

| 。网 | Java Script 语言 | 使用 |
| --- | --- | --- |
| `.Any()` | `.some()` | 检查序列中是否至少有一个元素满足条件 |
| `.All()` | `.every()` | 检查序列的所有元素是否满足条件 |
| `.Select()` | `.map()` | 将序列的所有元素转换成另一种表示形式 |
| `.Where()` | `.filter()` | 根据给定的条件过滤序列 |
| `.OrderBy()` | `.sort()` | 排序序列 |
| `.FirstOrDefault()` | `.find()` | 获取满足条件的第**个**元素 |

#### 参考文献

*   [MDN JavaScript 数组](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Array#)
*   [可枚举方法](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable?view=netcore-2.2)

在下一篇文章中，我们将看看如何实现流行的 Linq 方法，这些方法在 JavaScript 中没有对等的数组方法(例如`.Distinct()`)。