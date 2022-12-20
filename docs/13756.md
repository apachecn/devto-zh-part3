# 理解 JavaScript 数组数据收集的声明性方法的构建块

> 原文：<https://dev.to/olivermensahdev/understanding-the-functional-building-blocks-of-declarative-methods-on-javascript-array-data-collection-5hf2>

JavaScript 引入了新的集合集；地图、布景等。对于集合，我指的是保存多个数据的一些数据结构。其中一些数据结构更适合特定的问题。它们包含可迭代的元素，旨在让开发人员能够自由地使用泛型方法来实现要在其上执行的若干操作。

## 思维方式的步骤

在引入这些新集合之前，对象和数组是 JavaScript 的主力。数组在很多情况下都是最常用的。当我听到数组数据结构时，我想到的是按照步骤来思考。例如，在一个数组中存储一个人的详细信息列表需要您将它们存储在一个指定的索引中。类似地，当从一个数组中检索数据时(使用 people's details 示例)，只需遍历该数组并从所考虑的索引处获取数据。

### 命令式思维方式的步骤

命令式思维关注于事情是如何完成的。“如何”非常关注中间变量中的数据存储，用循环和条件语句管理控制流。假设我们有一个学生对象数组，每个对象都有一个姓名、年龄和电子邮件作为其成员属性，我们可以使用下面列出的代码来获取每个学生的年龄

```
 let students = [
    {name: "Oliver Mensah", age: 26, email: "omens@gmail.com"},
    {name: "Nana Adu", age: 16, email: null},
    {name: "Priscila Buer", age: 23, email: "prisy@gmail.com"}
]

function getUserAges(students){
    let ages  = [];
    for(i=0; i< students.length; i++){
        let student = students[i];
        if(student.age != null){
            age = student.age
            ages.push(age)
        }
    }
    return ages
}

console.log(getUserAges(students)); 
```

Enter fullscreen mode Exit fullscreen mode

你可以猜测上面的代码说明了什么；我们只创建一个中间变量(年龄数组)来保存结果。在遍历 student 数组的内容时，在将数据添加到中间变量之前会进行完整性检查。迭代完成后，返回结果集。

总之，它只是告诉我们如何检索年龄。这里做了很多工作。

### 陈述性思维

有了陈述性思维，你就可以陈述你想要的结果，而不用处理让你达到目的的每一个复杂的步骤。这是关于宣布你想要什么，而不是你想要怎么做。在许多情况下，这使得程序更短、更简单。它们通常也更容易阅读。

使用声明性方法，我们可以用更简洁的方式重构代码。

```
 let ages = students.map(user => {
    if(user.age != null) return user.age
})
console.log(ages); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们只是得到我们想要的，而不需要编写自己的循环，管理和访问索引。这是强大的，对不对？但是在幕后，JS 引擎必须做一些迭代来尽早访问每一个。反正那个我也不太懂。

Javascript 使得在数组集合上编写声明性代码变得很容易。不是重新发明轮子，我们将自己实现这些声明性的方法，只是为了理解它们是如何工作的。最后，我们将对这些是如何工作的有更深的了解。因此，在使用没有这些内置特性的其他编程语言时，能够为集合创建声明性方法。

## 仔细看看声明式方法。

我们在数组数据集合上使用了`map`方法来检索学生的年龄。它是一个高阶函数。因此，一个函数接受另一个函数。它的参数化函数是匿名的——一个没有命名的函数。这给了我们一个很好的基础。因此，在集合上创建声明性方法时，我们将需要一个高阶函数和同步[回调](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)，也就是匿名函数。高阶函数应用于集合，回调将在每个元素上执行。

### 从命令式到陈述式

以前，我们使用命令式方法来检索学生的年龄，然后应用声明式方法来获得相同的结果。我们可以继续使用指南来实现`map`；高阶函数和对每个元素的回调。

```
function map(items, func){
    let result = []
    for(item of items){
        result.push(func(item))
    }
    return result
}
console.log(map(students, (student) => student.age)) 
```

Enter fullscreen mode Exit fullscreen mode

我们创建了一个作用于整个数据集合的函数和另一个作用于每个元素的方法。很简单对吧。再来看看其他高阶函数。

### 其他函数方法/高阶函数。

我们看了一下`map`函数来声明性地处理数据收集。它只是对数组集合进行操作的强大的高阶函数之一。从这里开始，我们将深入研究在其他高阶函数下的工作原理；过滤、拒绝(可选)和减少。

#### [滤镜](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)

你可能会知道这个函数是做什么的。因此，它基本上根据某些条件从数据集合中过滤一些数据。例如，我们可以选择仅获取 20 岁以下的年龄。任何时候，你都必须根据特定的条件从数组集合中获取一些数据，然后必须想到`filter`高阶函数。

必要的是，为了只获取大于 20 年的年龄，我们将这样做；

```
function getUserAges(students){
    let ages  = [];
    for(i=0; i< students.length; i++){
        let student = students[i];
        if(student.age > 20){
            age = student.age
            ages.push(age)
        }
    }
    return ages
}
console.log(getUserAges(students)); 
```

Enter fullscreen mode Exit fullscreen mode

为了使用 ib 构建的声明性方法，我们可以调用 filter 方法来获取特定年龄的用户。

```
console.log(students.filter(user => user.age > 20)); 
```

Enter fullscreen mode Exit fullscreen mode

过滤器返回数组集合中传递的元素的新数组，因此我们必须映射结果以仅获得年龄，而不是元素本身。这就是声明性方法的威力所在。我们可以将尽可能多的方法链接或连接在一起来实现这一点。改进代码以仅获取年龄；

```
console.log(
 students.filter(user => user.age > 20)
         .map(user => user.age)
); 
```

Enter fullscreen mode Exit fullscreen mode

从前面编写我们自己的`map`函数的例子中汲取灵感。我们可以在这里采用相同的模式。这里不同的是，有一个条件。我们知道我们有一个函数处理整个集合，另一个函数处理单个项目。

```
function filter(items, func){
    let result = []
   for(item of items){
        if(func(item)){
            result.push(item)
        }
    }
    return result
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们的条件是，只有当匿名函数的返回值为真时，我们才能将这样的项目添加到集合中。当我们对此进行测试时，我们将得到与应用内置过滤器方法相同的结果。我们知道我们必须映射结果来得到我们的年龄。让我们这样做；

```
let studentAged20Plus = filter(students, (student) => student.age >20)    
console.log(map(studentAged20Plus, (student) =>student.age)); 
```

Enter fullscreen mode Exit fullscreen mode

#### 拒绝(可选)

`reject`函数没有普通的 JavaScript，但它与`filter`类似，只是方向相反。所以我们可以用逻辑操作符包装`filter`函数来反转结果。让我们这样做；

```
 function reject(items, func){
    return filter(items, function (item){
        return ! func(item);
    })
}
let studentAged20Plus = reject(students, 
(student) => student.age >20)
console.log(map(studentAged20Plus, 
(student) => student.age)); 
```

Enter fullscreen mode Exit fullscreen mode

所以这给出了与`filter`相反的结果。

#### [减少](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

使用`reduce`，我们完全从项目列表中产生新的一个项目值。它通过累加每个当前项并在稍后返回累加值来实现这一点。

假设我们想得到学生的平均年龄。用命令式的方法，你会以这种方式处理问题；

```
function averageAge(students){
    let sumAge  = 0;
    for(i=0; i< students.length; i++){
        let student = students[i];
        sumAge += student.age
    }
    return sumAge/students.length
}
console.log(averageAge(students)) 
```

Enter fullscreen mode Exit fullscreen mode

对于内置的声明性函数，我们将不得不使用 reduce 方法来总结它们。

```
console.log(students.reduce((acc, curr) 
=> acc + curr.age, 0) / students.length); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们有一个起始值为`0`的累加器，我们不断地将每个项目的年龄添加到累加器中。然后将结果除以学生总数。非常简洁优雅。

如果我们要编写这个声明性的方法，我们需要记住累加器、单个值、累加器起始值和一个作用于每个元素的函数。

```
function reduce(items, func, initialValue){
    let accumulator = initialValue;
    for(item of items){
        accumulator = func(accumulator, item);
    }
    return accumulator;
}
console.log(reduce(students,(accum, curr)
=>accum + curr.age, 0)) 
```

Enter fullscreen mode Exit fullscreen mode

有了这两个概念；高阶函数和回调函数，我们也能够实现`reduce`函数。

# 结论。

我希望您会发现这种在数据集合上编写声明性方法的方法非常有用。这里的关键是注意模式。因此，应该在什么时候调用匿名函数。一旦理解了这一点，您就可以采用任何允许回调实现的编程语言中的概念。

期待收到您的反馈或更多关于如何在数据收集上实现声明性方法的想法。享受阅读。

## 进一步阅读

[索引收藏](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Indexed_collections)

## 保持联系

当我们继续学习和探索更多关于软件工程的知识时，让我们保持联系。别忘了在推特或 T2【LinkedIn】和我联系