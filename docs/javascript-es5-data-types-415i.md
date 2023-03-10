# JavaScript (ES5)数据类型

> 原文：<https://dev.to/martyhimmel/javascript-es5-data-types-415i>

## 声明变量

在我们进入数据类型之前，让我们看一下声明变量。变量是某种东西的容器——更确切地说，是一种数据类型的容器。一个变量声明由 2 或 3 部分组成——`var`关键字、变量名和用等号赋给变量的值(这是可选的)。JavaScript 是一种松散类型的语言，这意味着在创建变量时不必声明数据类型——不管数据类型是什么，都会使用`var`关键字。

这里有几个创建变量的例子:

```
var firstName = "John";
var age = 42;
var notInitialized; 
```

Enter fullscreen mode Exit fullscreen mode

前两个变量在创建时就有一个赋值。这称为初始化。第三个变量只创建了一个名为`notInitialized`的变量，但没有给它赋值。这个变量可以在代码中使用，但是在给它赋值之前，它的值是`undefined`——稍后会详细介绍。

## 数据类型

数据类型处理事物的存储方式。在 JavaScript 中，有 6 种数据类型:

*   线
*   数字
*   布尔代数学体系的
*   空
*   不明确的
*   目标

### 字符串

字符串是文本的集合。它可以是单个字符、一行文本、一个段落等等。要将字符串赋给变量，请用单引号或双引号将文本括起来。在 JavaScript 中，单引号和双引号之间没有真正的区别。唯一重要的是，如果你在字符串中使用引号。在这种情况下，您可以使用其他类型的引号作为包装器，或者对字符串中的引号进行转义(`\"`或`\'` -反斜杠是转义字符)。这里有几个例子。

```
var fullName = 'John Doe';
var stringNumber = '13';
var firstLetter = 'a';
var unescapedContraction = "it's";
var escapedContraction = 'it\'s'; 
```

Enter fullscreen mode Exit fullscreen mode

### 数字

一个数字听起来就像是一个数字。在 JavaScript 中，数字数据类型被视为双精度浮点 64 位数字。更多技术细节，请查看 Mozilla 的文档。

数字可以是整数或小数，正数或负数。这里有一些数字变量的例子。

```
var answer = 42;
var pi = 3.1415926;
var nothing = 0;
var negative = -18748942305; 
```

Enter fullscreen mode Exit fullscreen mode

关于数字的一个重要注意事项是，当你赋值时，*不要用引号将数字括起来。如果这样做，它将被指定为一个字符串，这可能会产生一些意想不到的后果(特别是在添加数字时)。比如:* 

```
var numString = '7';
var num = 82;
num + numString; // "827" 
```

Enter fullscreen mode Exit fullscreen mode

加号既是数学加法运算符，也是字符串连接运算符。在这种情况下，因为其中一个变量是字符串，JavaScript 将其解释为串联运算符，而不是加法。

### 布尔

布尔值要么为真，要么为假，就是这样。这些通常被用作某些东西的标志。

```
var hasPets = true;
var isPlaying = false; 
```

Enter fullscreen mode Exit fullscreen mode

像数字一样，在给布尔值赋值时不要使用引号。否则，它将被视为字符串。

### Null

被赋值为空值的变量表示该变量存在，但它显式地没有值或类型。在 JavaScript 中，null 必须显式设置为变量。

```
var nullValue = null; 
```

Enter fullscreen mode Exit fullscreen mode

### 未定义

具有未定义值的变量意味着该变量存在，但尚未赋值或类型。这不同于 null。Null 显式不赋值，undefined 不赋值，是任何未初始化变量的默认状态。

```
var notInitialized;
console.log(notInitialized); // undefined
console.log(typeof notInitialized); // undefined 
```

Enter fullscreen mode Exit fullscreen mode

*注意:`typeof`是一个内置的 JavaScript 函数，可以让你检查数据类型。它返回数据类型的名称。有趣的是，`typeof null`返回了`object`——这是第一版 JavaScript 的一个错误，为了保持与遗留代码的向后兼容性，这个错误从未被修复。*

### 物体

对象是属性及其值或键/值对的集合。你可以把物体想象成字典。字典是单词(属性)及其定义(值)的集合。设置一个对象与其他数据类型有点不同。对象本身是使用左花括号和右花括号创建的。在对象内部，属性是在没有`var`关键字的情况下创建的(只是命名它们)，并且不用等号来设置值，而是用冒号来设置值。多个属性用逗号分隔。

属性值可以是任何数据类型，包括另一个对象。这里有几个例子，一个是空对象，一个包含多种数据类型。

```
var emptyObject = {};
var person = {
  firstName: 'Jane',
  lastName: 'Doe',
  age: 27,
  hasPassport: true,
  kidsNames: ['Jackie', 'Joe', 'Julie'],
  pets: {
    numberOfDogs: 2,
    numberOfCats: 1
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

要从对象中获取值，请使用点标记法(即 object.property)访问属性。

```
console.log(person.firstName); // Jane
console.log(person.age); // 27
console.log(person.hasPassport); // true
console.log(person.pets.numberOfDogs); // 2 
```

Enter fullscreen mode Exit fullscreen mode

### 阵列

数组是一种特殊类型的对象(在数组上调用`typeof`会返回`object`，这就是为什么它没有被列为一种数据类型，但是它与对象有足够的不同，它有自己的部分。它更像是一个数据容器，保存着一个列表。像对象一样，数组可以包含所有不同类型的数据。主要区别是数组是一个数字索引列表，而不是属性和值的列表。使用数组时，只需给它赋值。使用方括号创建数组。

```
var emptyArray = [];
var animals = ['cat', 'dog', 'owl', 'horse', 'lion'];
var mixedArray = ['John', 31, false, ['Jake', 'Jerry'], {cars: 2}]; 
```

Enter fullscreen mode Exit fullscreen mode

若要访问数组中的值，请使用括号符号(array[indexNumber])。数组是零索引的，这意味着数组的第一个元素的索引为 0。数组有一个名为`length`的属性，它返回数组中元素的数量。当访问数组的最后一个元素或循环访问数组时，length 属性特别有用。下面是一些使用上面的`mixedArray`例子访问数组元素的例子。

```
console.log(mixedArray[0]); // John
console.log(mixedArray[2]); // false
console.log(mixedArray[4].cars); // 2
console.log(mixedArray.length); // 5
console.log(mixedArray[mixedArray.length - 1]); // {cars: 2}
console.log(mixedArray[3][1]); // Jerry 
```

Enter fullscreen mode Exit fullscreen mode

因为数组是零索引的，所以第一个元素是 0，最后一个元素是`array.length - 1`。在本例中，mixedArray 有 5 个元素(一个字符串、一个数字、一个布尔值、一个数组和一个对象)，因此 mixedArray.length 返回 5。如果你试图访问 mixedArray[5]，你将试图访问数组的第六个元素——这就是为什么如果你试图直接访问最后一个元素，你需要从长度中减去 1。

在最后一条 log 语句中，您将看到两组括号。数组内部的数组称为多维数组。对于数组中的每个深度级别，需要另一个括号和索引。在这种情况下，`mixedArray[3]`让我们可以访问['Jake '，' Jerry']数组。附加第二个括号和元素号(`mixedArray[3][1]`)可以让我们访问内部数组索引 1 - `Jerry`处的值。