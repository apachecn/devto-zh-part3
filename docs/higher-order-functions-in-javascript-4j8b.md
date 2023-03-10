# 高阶函数 JavaScript JavaScript 中的高阶函数

> 原文：<https://dev.to/damcosset/higher-order-functions-in-javascript-4j8b>

## 简介

在 Javascript 中，函数是值(一等公民)。这意味着它们可以被赋给一个变量和/或作为一个值传递。

```
let random = function(){
    return Math.random()
}

let giveMeRandom = random // assigning random to a variable 
```

Enter fullscreen mode Exit fullscreen mode

这一点知识允许我们用这种语言编写函数式编程。在函数式编程中，我们大量使用高阶函数。

## 高阶函数？

高阶函数是以其他函数为自变量的函数*或*返回函数作为结果。

将另一个函数作为参数通常被称为*回调函数*，因为它被高阶函数回调。这是 Javascript 大量使用的一个概念。

例如，数组上的 *map* 函数是一个高阶函数。*映射*函数将一个函数作为参数。

```
 const double = n => n * 2

[1, 2, 3, 4].map(double) // [ 2, 4, 6, 8 ] 
```

Enter fullscreen mode Exit fullscreen mode

或者，用匿名函数:

```
[1, 2, 3, 4].map(function(n){
    return n * 2
}) // [ 2, 4, 6, 8 ] 
```

Enter fullscreen mode Exit fullscreen mode

*map* 函数是该语言内置的许多高阶函数之一。*排序*、*归约*、*过滤*、 *forEach* 是语言中内置的高阶函数的其他例子。

高阶函数允许您编写更简单、更优雅的代码。让我们看看如果没有这样的抽象，上面的代码会是什么样子。让我们用一个循环来代替*映射*函数:

```
let array = [1, 2, 3, 4]
let newArray = []

for(let i = 0; n < array.length; i++) {
    newArray[i] = array[i] * 2
}

newArray // [ 2, 4, 6, 8 ] 
```

Enter fullscreen mode Exit fullscreen mode

## 构图的力量

尽可能使用高阶函数的一个很大的好处是合成。

我们可以创建更小的函数，只处理一部分逻辑。然后，我们用不同的小函数组成更复杂的函数。

这种技术减少了错误，使我们的代码更容易阅读和理解。

通过学习使用高阶函数，您可以开始编写更好的代码。

## 举例

让我们试一个例子。假设我们有一个教室的成绩列表。我们的教室有 5 个女孩，5 个男孩，他们每个人的分数都在 0 到 20 之间。

```
var grades = [
    {name: 'John', grade: 8, sex: 'M'},
    {name: 'Sarah', grade: 12, sex: 'F'},
    {name: 'Bob', grade: 16, sex: 'M'},
    {name: 'Johnny', grade: 2, sex: 'M'},
    {name: 'Ethan', grade: 4, sex: 'M'},
    {name: 'Paula', grade: 18, sex: 'F'},
    {name: 'Donald', grade: 5, sex: 'M'},
    {name: 'Jennifer', grade: 13, sex: 'F'},
    {name: 'Courtney', grade: 15, sex: 'F'},
    {name: 'Jane', grade: 9, sex: 'F'}
] 
```

Enter fullscreen mode Exit fullscreen mode

我想知道一些关于这个的事情:

*   这间教室的平均成绩
*   男生的平均成绩
*   女生的平均成绩
*   男孩中的高音
*   女孩中的高音

我们将尝试使用高阶函数来得到一个简单易读的程序。让我们从编写可以一起工作的简单函数开始:

```
let isBoy = student => student.sex === 'M'

let isGirl = student => student.sex === 'F'

let getBoys = grades => (
    grades.filter(isBoy)
)

let getGirls = grades => (
    grades.filter(isGirl)
)

let average = grades => (
    grades.reduce((acc, curr) => (
        acc + curr.grade
    ), 0) / grades.length
)

let maxGrade = grades => (
    Math.max(...grades.map(student => student.grade))
)

let minGrade = grades => (
    Math.min(...grades.map(student => student.grade))
) 
```

Enter fullscreen mode Exit fullscreen mode

我写了 7 个函数，每个函数都有一个任务，而且只有一个任务。

isBoy 和 *isGirl* 负责检查一个学生是男生还是女生。

getBoys 和 *getGirls* 负责让所有的男生或女生离开教室。

*maxGrade* 和 *minGrade* 负责获取某些数据中最大和最小的等级。

最后， *average* 负责计算一些数据的平均等级。

注意， *average* 函数还不知道它应该处理的数据类型。这就是构图的妙处。我们可以在不同的地方重用我们的代码。我可以把这个函数和其他函数一起插入。

现在，我们有了编写高阶函数所需要的东西:

```
 let classroomAverage = average(grades) // 10.2
let boysAverage = average(getBoys(grades)) // 7
let girlsAverage = average(getGirls(grades)) // 13.4
let highestGrade = maxGrade(grades) // 18
let lowestGrade = minGrade(grades) // 2
let highestBoysGrade = maxGrade(getBoys(grades)) // 16
let lowestBoysGrade = minGrade(getBoys(grades)) // 2
let highestGirlsGrade = maxGrade(getGirls(grades)) // 18
let lowestGirlsGrade = minGrade(getGirls(grades)) // 9 
```

Enter fullscreen mode Exit fullscreen mode

注意，外部函数，例如 *average* ，总是将内部函数的输出作为输入。因此，合成的唯一条件是确保输出和输入匹配。

因为每个函数只负责一件事，这使得我们的代码更容易调试和测试。

## 作文规则！