# 在 JavaScript 中操作数组

> 原文：<https://dev.to/bolajiayodeji/manipulating-arrays-in-javascript-1lk2>

数组已经成为任何编程语言的重要组成部分。大多数时候我们需要在数组上做一些操作，因此有了这篇文章。

在本文中，我将向您展示在 JavaScript [^^]中操作数组的各种方法

[![](img/ae599f55bc9e976842618fee51eb8a94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---501sHoz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2AEK3RyHqvMS-ZIy9UyNMxTA.png)

* * *

### JavaScript 中数组是什么？

在我们继续之前，您需要理解数组的真正含义。

> 在 JavaScript 中，数组是用来存储不同数据类型的变量。它基本上将不同的元素存储在一个盒子里，以后可以用变量
> 访问。

声明数组:

```
let myBox = [];   // Initial Array declaration in JS 
```

Enter fullscreen mode Exit fullscreen mode

数组可以包含多种数据类型

```
let myBox = ['hello', 1, 2, 3, true, 'hi']; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

可以通过使用几个被称为**方法的动作来操作数组。**这些方法中的一些
允许我们添加、删除、修改数组以及做更多的事情。

我将在本文中向您展示一些，让我们开始吧:)

> 注意:我在这篇文章中使用了**箭头功能**，如果你不知道这是什么意思，
> 你应该在这里阅读
> 。
> 箭头功能是 **ES6 的一个特征**。

* * *

### toString()

JavaScript 方法`toString()`将数组转换成由逗号
分隔的字符串。

```
let colors = ['green', 'yellow', 'blue'];
colors.toString();

console.log(colors); // "green,yellow,blue" 
```

Enter fullscreen mode Exit fullscreen mode

### 加入()

JavaScript `join()`方法将所有数组元素组合成一个字符串。

它类似于`toString()`方法，但是这里您可以指定分隔符
来代替默认的逗号。

```
let colors = ['green', 'yellow', 'blue'];
colors.join('-');

console.log(colors); // "green-yellow-blue" 
```

Enter fullscreen mode Exit fullscreen mode

### 串联

这个方法将两个数组组合在一起，或者向一个数组中添加更多的元素，然后
返回一个新的数组。

```
let firstNumbers = [1, 2, 3];
let secondNumbers = [4, 5, 6];

let merged = firstNumbers.concat(secondNumbers);

console.log(merged); // [1, 2, 3, 4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

### 推()

这个方法将条目添加到一个数组的末尾，**改变**原来的
数组。

```
let browsers = ['chrome', 'firefox', 'edge'];
browsers.push('safari', 'opera mini');

console.log(browsers); 
// ["chrome", "firefox", "edge", "safari", "opera mini"] 
```

Enter fullscreen mode Exit fullscreen mode

### 流行()

这个方法移除数组的最后一项，然后**返回**

```
let browsers = ['chrome', 'firefox', 'edge'];
browsers.pop(); // "edge"

console.log(browsers); // ["chrome", "firefox"] 
```

Enter fullscreen mode Exit fullscreen mode

### 移位()

这个方法移除数组的第一项，**返回它**

```
let browsers = ['chrome', 'firefox', 'edge'];
browsers.shift(); // "chrome"

console.log(browsers); // ["firefox", "edge"] 
```

Enter fullscreen mode Exit fullscreen mode

### 未移位()

这个方法在数组的开头添加一个条目，然后**改变**原来的
数组。

```
let browsers = ['chrome', 'firefox', 'edge'];
browsers.unshift('safari');

console.log(browsers); //  ["safari", "chrome", "firefox", "edge"] 
```

Enter fullscreen mode Exit fullscreen mode

> 您也可以一次添加多个项目

### 拼接()

这个方法**通过添加、移除和插入
元素来改变**一个数组。

语法是:

```
array.splice(index[, deleteCount, element1, ..., elementN]) 
```

Enter fullscreen mode Exit fullscreen mode

*   `Index`这里是删除数组中元素的起点
*   `deleteCount`是要从该索引中删除的元素数
*   `element1, …, elementN`是要添加的元素

**移除项目**

> 在运行 **splice()** 之后，它返回移除了项目的数组，
> 将它从原始数组中移除。

```
let colors = ['green', 'yellow', 'blue', 'purple'];
colors.splice(0, 3);
console.log(colors); // ["purple"]
// deletes ["green", "yellow", "blue"] 
```

Enter fullscreen mode Exit fullscreen mode

> **NB** :删除计数不包括范围内的最后一个索引。

如果没有声明第二个参数，从给定的
索引开始的每个元素都将从数组中删除:

```
let colors = ['green', 'yellow', 'blue', 'purple'];
colors.splice(3);
console.log(colors); // ["green", "yellow", "blue"]
// deletes ['purple'] 
```

Enter fullscreen mode Exit fullscreen mode

在下一个例子中，我们将从数组中删除 3 个元素，并用更多的元素来替换它们
:

```
let schedule = ['I', 'have', 'a', 'meeting', 'tommorrow'];
// removes 4 first elements and replace them with another
schedule.splice(0, 4, 'we', 'are', 'going', 'to', 'swim');
console.log(schedule); 
// ["we", "are", "going", "to", "swim", "tommorrow"] 
```

Enter fullscreen mode Exit fullscreen mode

**添加项目**

要添加项目，我们需要将`deleteCount`设置为零

```
let schedule = ['I', 'have', 'a', 'meeting', 'with'];
// adds 3 new elements to the array
schedule.splice(5, 0, 'some', 'clients', 'tommorrow');
console.log(schedule); 
// ["I", "have", "a", "meeting", "with", "some", "clients", "tommorrow"] 
```

Enter fullscreen mode Exit fullscreen mode

### 切片()

> 这种方法和`splice()`类似，但又很不一样。它返回子数组
> 而不是子字符串。

这个方法**复制**一个数组的给定部分，并将复制的部分作为新数组的
返回。**它不改变原来的阵列。**

语法是:

```
array.slice(start, end) 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个基本的例子:

```
let numbers = [1, 2, 3, 4]
numbers.slice(0, 3)
// returns [1, 2, 3]

console.log(numbers) // returns the original array 
```

Enter fullscreen mode Exit fullscreen mode

使用`slice()`的最好方法是将它赋给一个新变量。

```
let message = 'congratulations'
const abbrv = message.slice(0, 7) + 's!'; 
console.log(abbrv) // returns "congrats!" 
```

Enter fullscreen mode Exit fullscreen mode

### 分割()

这个方法用于**字符串**。它将一个字符串分成子字符串，然后
将它们作为数组返回。

下面是语法:

```
string.split(separator, limit); 
```

Enter fullscreen mode Exit fullscreen mode

*   这里的`separator`定义了如何用逗号分割字符串。
*   `limit`决定要执行的分割数量

```
let firstName = 'Bolaji';
// return the string as an array
firstName.split() // ["Bolaji"] 
```

Enter fullscreen mode Exit fullscreen mode

另一个例子:

```
let firstName = 'hello, my name is bolaji, I am a dev.';
firstName.split(',', 2); // ["hello", " my name is bolaji"] 
```

Enter fullscreen mode Exit fullscreen mode

> **NB:** 如果我们声明一个空数组，像这样: *`firstName.split('');`* 那么
> 字符串中的每一项都会被划分为子字符串:

```
let firstName = 'Bolaji';
firstName.split('') // ["B", "o", "l", "a", "j", "i"] 
```

Enter fullscreen mode Exit fullscreen mode

### indexOf()

这个方法在一个数组中寻找一个条目，并返回找到它的索引
，否则返回`-1`

```
let fruits = ['apple', 'orange', false, 3]
fruits.indexOf('orange'); // returns 1
fruits.indexOf(3); // returns 3
friuts.indexOf(null); // returns -1 (not found) 
```

Enter fullscreen mode Exit fullscreen mode

### lastIndexOf()

这个方法的工作方式与 indexOf() 相同，除了它是从
右到左工作的。它返回找到该项的最后一个索引

```
let fruits = ['apple', 'orange', false, 3, 'apple']
fruits.lastIndexOf('apple'); // returns 4 
```

Enter fullscreen mode Exit fullscreen mode

### 滤镜()

如果数组中的元素通过了某个
条件，这个方法就会创建一个新的数组。

语法是:

```
let results = array.filter(function(item, index, array) {
  // returns true if the item passes the filter
}); 
```

Enter fullscreen mode Exit fullscreen mode

示例:

检查来自尼日利亚的用户

```
const countryCode = ['+234', '+144', '+233', '+234'];
const nigerian = countryCode.filter( code => code === '+234');
console.log(nigerian); // ["+234", "+234"] 
```

Enter fullscreen mode Exit fullscreen mode

### 地图()

此方法通过操作数组中的值来创建一个新数组。

示例:

在页面上显示用户名。(基本好友列表显示)

```
const userNames = ['tina', 'danny', 'mark', 'bolaji'];
const display = userNames.map(item => {
 '<li>' + item + '</li>';
})
const render = '<ul>' + display.join('') + '</ul>';

document.write(render); 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/c6c78d7052f84539fa8053d0bb26f0db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IQYaM12C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2AobuBZKFb5vKmUP7D4TX2XA.png)

再比如:

```
// adds dollar sign to numbers
const numbers = [10, 3, 4, 6];
const dollars = numbers.map( number => '$' + number);
console.log(dollars);
// ['$10', '$3', '$4', '$6']; 
```

Enter fullscreen mode Exit fullscreen mode

### 减少()

这种方法适用于计算总数。

**reduce()** 用于计算基于数组的单个值。

语法是:

```
let value = array.reduce(function(previousValue, item, index, array) {
  // ...
}, initial); 
```

Enter fullscreen mode Exit fullscreen mode

示例:

> 要循环遍历一个数组并将数组中的所有数字相加，我们可以使用 for
> of 循环。

```
const numbers = [100, 300, 500, 70];
let sum = 0;
for (let n of numbers) {
sum += n;
}
console.log(sum); 
```

Enter fullscreen mode Exit fullscreen mode

下面是如何用`reduce()`做同样的事情

```
const numbers = [100, 300, 500, 70];
const sum = numbers.reduce((accummulator, value) =>
accummulator + value
, 0);

console.log(sum); // 970 
```

Enter fullscreen mode Exit fullscreen mode

> 如果省略初始值，默认情况下，*总计*将从数组中的第一个
> 项开始。

```
const numbers = [100, 300, 500, 70];
const sum = numbers.reduce((accummulator, value) => accummulator + value);

console.log(sum); // still returns 970 
```

Enter fullscreen mode Exit fullscreen mode

下面的代码片段展示了 **reduce()** 方法如何处理所有四个
参数。

**来源:MDN 文档**

[![](img/0dfaf335975b7a1dd491fb2109d841d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iqZt18PG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2ACbd9qR_vy71qZjEQCFpCLQ.png)

更多关于 **reduce()** 方法及其各种使用方法的见解可以在
找到[这里](https://medium.freecodecamp.org/reduce-f47a7da511a9)和
[这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)。

### forEach()

这个方法很适合迭代数组。

它对数组中的所有项应用一个函数

```
const colors = ['green', 'yellow', 'blue'];

colors.forEach((item, index) => console.log(index, item));
// returns the index and the every item in the array
// 0 "green"
// 1 "yellow"
// 2 "blue" 
```

Enter fullscreen mode Exit fullscreen mode

迭代可以在不传递索引参数的情况下完成

```
const colors = ['green', 'yellow', 'blue'];

colors.forEach((item) => console.log(item));
// returns every item in the array
// "green"
// "yellow"
// "blue" 
```

Enter fullscreen mode Exit fullscreen mode

### 每()

该方法检查数组中的所有项是否都通过了指定的条件，如果通过了，
返回`true`，否则返回`false`。

> 检查是否所有数字都是正数

```
const numbers = [1, -1, 2, 3];
let allPositive = numbers.every((value) => {
 value >= 0;
})

console.log(allPositive); 
```

Enter fullscreen mode Exit fullscreen mode

### 有的()

这个方法检查数组中的一个项目(一个或多个)是否通过指定的
条件，如果通过则返回 true，否则返回 false。

> 检查是否至少有一个数字是正数

```
const numbers = [1, -1, 2, 3];

let atLeastOnePositive = numbers.some((value) => {
 value >= 0;
})
console.log(atLeastOnePositive); 
```

Enter fullscreen mode Exit fullscreen mode

### 包括()

此方法检查数组是否包含某一项。它类似于
`.some()`，但是它不是寻找一个特定的条件来传递，而是检查
数组是否包含一个特定的项。

```
let users = ['paddy', 'zaddy', 'faddy', 'baddy'];
users.includes('baddy'); // returns true 
```

Enter fullscreen mode Exit fullscreen mode

如果没有找到该项，则返回`false`

* * *

还有更多的数组方法，这只是其中的一部分。此外，还有许多可以在数组上执行的其他操作，请尝试查看 MDN 文档
[这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/)
以获得更深入的见解。

### 总结

*   **toString()** 将数组转换为逗号分隔的字符串。
*   **join()** 将所有数组元素组合成一个字符串。
*   **concat** 将两个数组合并在一起或者向一个数组中添加更多的项，然后返回一个新的数组。
*   **push()** 将项目添加到数组的末尾，**改变**原始数组。
*   **pop()** 移除数组的最后一项，**返回**它
*   **shift()** 删除数组的第一项，**返回**它
*   **unshift()** 将一个(或多个)项添加到数组的开头，**改变**原始数组。
*   **splice()** **改变**一个数组，通过添加、删除和插入元素。
*   **slice()** **复制**数组的给定部分，并将复制的部分作为新数组返回。**它不改变原来的阵列。**
*   **split()** 将一个字符串分割成子字符串，并将它们作为数组返回。
*   **indexOf()** 在一个数组中寻找一个条目，并返回找到它的索引否则返回`-1`
*   **lastIndexOf()** 从右到左查找项目，并返回找到该项目的最后一个索引。
*   filter() 如果一个数组中的元素满足一定的条件，则创建一个新的数组。
*   map() 通过操作数组中的值来创建一个新的数组。
*   **reduce()** 基于数组计算单个值。
*   **forEach()** 遍历数组，对数组中的所有项应用函数
*   **every()** 检查数组中的所有项是否通过指定的条件，如果通过则返回 true，否则返回 false。
*   **some()** 检查数组中的一项(一个或多个)是否通过指定的条件，如果通过则返回 true，否则返回 false。
*   **includes()** 检查数组中是否包含某项。

* * *

让我们把它包在这里；数组是强大的，使用方法来操作它们
创造了现实世界应用程序使用的算法。

让我们创建一个小函数，将文章标题转换成一个
urlSlug。

> **URL slug** 是你网站上特定页面或帖子的确切地址。

当你在 **[介质](https://medium.com)** 或任何其他写作平台上写文章时，你的文章
标题会自动转换为一个空字符，字符
转换为小写，标题中的每个单词由连字符分隔。

这是一个基本函数，它使用了我们刚刚学过的一些方法。

```
const url = 'https://bolajiayodeji.com/'
const urlSlug = (postTitle, category) => {
let postUrl = postTitle.toLowerCase().split(' ');
let postSlug = `${url}` + category + '/' + postUrl.join('-');
return postSlug;
}
let postTitle = 'Introduction to Chrome Lighthouse'
let category = 'dt'
console.log(urlSlug(postTitle, category));

// https://bolajiayodeji.com/dt/introduction-to-chrome-lighthouse 
```

Enter fullscreen mode Exit fullscreen mode

在`postUrl`中，我们将字符串转换成小写，然后使用 **split()**
方法将字符串转换成子字符串，并在数组中返回它

```
["introduction", "to", "chrome", "lighthouse"] 
```

Enter fullscreen mode Exit fullscreen mode

在`post slug`中，我们用连字符连接返回的数组，然后将它
连接到类别字符串和主字符串`url`。

```
let postSlug = `${url}` + category + '/' + postUrl.join('-');
postUrl.join('-') // introduction-to-chrome-lighthouse 
```

Enter fullscreen mode Exit fullscreen mode

就这样，很简单，对吧？:)

* * *

如果你刚刚开始使用 JavaScript，你应该检查这个库
[这里](https://github.com/BolajiAyodeji/js-code-snippets)，我正在编译一个基本 JavaScript 片段的列表
，范围从

*   数组
*   控制流
*   师
*   功能
*   目标
*   经营者

感谢您的阅读，有任何问题或看法吗？让我们在评论中讨论。