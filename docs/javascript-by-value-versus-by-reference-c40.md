# JavaScript:按值还是按引用

> 原文：<https://dev.to/azrulaziz/javascript-by-value-versus-by-reference-c40>

**为什么知道这个话题很重要？**

*   为了理解当我们给一个变量赋值时会发生什么
*   为了避免潜在的错误发生

* * *

在 JavaScript 中，当我们声明一个变量并给它赋值时，底层的 JavaScript 引擎将评估给定值的类型并决定如何将该值放入内存。JavaScript 有两种方式将那个值放入内存:通过值&通过引用。

#### 按值

首先，我们需要知道 JavaScript 中的所有原始类型都将通过值进行复制。
JavaScript 中的原语类型有:

1.  线
2.  数字
3.  布尔代数学体系的
4.  空
5.  不明确的

那么“按价值”是什么意思呢？😕让我们回顾一下这个例子:

```
// Declare a variable and pass a primitive value to it
let favoriteBand = "Underoath"

// Create another variable and pass favoriteBand as the value
let sameButDifferent  = favoriteBand

// Log the result
console.log(favoriteBand) // "Underoath"
console.log(sameButDifferent) // "Underoath" 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们声明了一个名为 ***favoriteBand*** 的变量，并将字符串“*under oard*”作为其值传递，这是一个原始类型。当我们这样做时，JavaScript 为这个变量的值创建一个新的内存位置。
然后，我们声明了另一个名为***same but different***的变量，并将变量 ***favoriteBand*** 作为其值传递。
当我们记录这两个变量时，它们都返回字符串“*under oard*”作为它们的值。

这里需要注意的是，尽管两个变量记录了相同的结果，但它们位于完全不同的内存位置。当我们声明变量***same but different***时，JavaScript 引擎为这个变量的值创建了一个完全独立的内存位置。

现在，如果我们试图改变任何变量的一个值:

```
// Reassign the value for the variable favoriteBand
let favoriteBand = "Saosin"

console.log(favoriteBand) // "Saosin"
console.log(sameButDifferent) // "Underoath" 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到它没有反映出另一个，因为两者本质上是不一样的。因此，如果我们声明一个变量，并将其值设置为数字、布尔或任何原始类型，这个“按值”规则将适用于它们。

#### 被引用

相比之下，JavaScript 中的所有对象都将被引用复制。

JavaScript 中的对象包括:

1.  对象(显然)
2.  排列
3.  功能

让我们看看“通过引用”是什么意思。考虑下面的例子:

```
// Declare a variable and assign an object to it
let firstAlbum = {
    single: 'In Division'
}

// Create another variable and pass firstAlbum as the value
let secondAlbum = firstAlbum

// Log the result
console.log(firstAlbum) // {single: 'In Division'}
console.log(secondAlbum) // {single: 'In Division'} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看一下这段代码。首先我们声明了一个名为 ****firstAlbum**** 的变量，并向其传递了一个
对象。JavaScript 引擎现在将为这个对象
创建一个新的内存位置。
然后我们声明了另一个变量 ****secondAlbum**** 并将变量****first album****
作为其值传递。现在 JavaScript 将看到我们传递给 ****secondAlbum**** 的值包含一个已经存在于内存中的对象。
不是创建一个新的内存位置，它只是将这个变量指向为变量 ****firstAlbum**** 创建的同一个对象
。

现在让我们改变对象内部的一个属性值:

```
// reassign a value inside the Object
secondAlbum.single = 'Paperlung'

// log the result
console.log(firstAlbum) // {single: 'Paperlung'}
console.log(secondAlbum) // {single: 'Paperlung'} 
```

Enter fullscreen mode Exit fullscreen mode

当我们记录变量时，它显示即使我们只对单个变量进行
更改，更改也会反映在两个变量上。这是因为它们本质上是同一个对象，并且引用同一个内存位置。

这里还有一个例子:

```
// Pass object as parameters
function changeSingle(obj) {
    obj.single = 'Seven Years';
}

// Call the function
changeSingle(firstAlbum);

// log the result for both variables
console.log(firstAlbum); // {single: 'Seven Years'}
console.log(secondAlbum); // {single: 'Seven Years'} 
```

Enter fullscreen mode Exit fullscreen mode

我们将对象作为参数传递到函数中，然后在对象中改变属性值。这种变化反映在两个变量上，因为我们仍然在改变同一个对象。

最后，让我们给变量 ****firstAlbum**** 分配一个全新的对象:

```
// reassign a new object to the firstAlbum variable
firstAlbum = {
    producer: "Mark Goldman"
}

// log the result for both variables
console.log(firstAlbum); // {producer: 'Mark Goldman'}
console.log(secondAlbum); // {single: 'Seven Years'} 
```

Enter fullscreen mode Exit fullscreen mode

现在 JavaScript 看到创建了一个新对象，所以它会专门为这个对象分配另一个内存位置。这个规则同样适用于函数和数组。

简单来说，每当我们创建一个原始类型并传递它时，总会在内存中创建一个新值。当我们创建一个对象并传递它时，如果它已经存在，它将总是指向同一个内存位置。