# 我到底怎么用滤镜？

> 原文：<https://dev.to/liltechnomancer/how-the-hell-do-i-use-filter-2e82>

这是关于最有用的数组方法、`map`、`reduce`和`filter`的三部分系列的第二部分。我们接下来将处理`reduce`。关于`map`的帖子可以在[这里](https://lvrbrtsn.com//blog/how-the-hell-do-i-use-map)找到。然而今天我们将谈论所有关于`filter`的话题！Filter 是一种非常有用的方法，可以从列表中删除不符合特定标准的元素。你可能会问标准是什么？你想要什么都行！这之所以成为可能，是因为`filter`像`map`一样是一个**高阶函数**，我们在[上一篇文章](https://lvrbrtsn.com//blog/how-the-hell-do-i-use-map)中讨论过，但基本上它只是意味着它是一个以另一个函数作为参数的函数。看看下面这个例子。

```
// Dummy data to work with
const students = [ { name: 'Leroy Jenkins' grade: 55 },
                   { name: 'Tommy Pickles' grade: 98 },
                   { name: 'Mike Tyson' grade: 85 },
                   { name: 'Donnie Darko' grade: 90 } ]

// Our filtering criteria. Which can be any
// function that returns true and false
const isPassing = (student) => student.grade >= 65

// Usage of filter.
const onlyWinners = students.filter(isPassing)
// => [ { name: 'Tommy Pickles' grade: 98 },
//      { name: 'Mike Tyson' grade: 85 },
//      { name: 'Donnie Darko' grade: 90 } ] 
```

Enter fullscreen mode Exit fullscreen mode

所以使用 filter 的唯一条件是你提供给它一个返回`boolean`的函数。
然后它将**迭代**你的数组，通过它运行你提供的函数。当它传入一个项目(或者在本例中是 student)并且函数返回`true`时，它为正在创建的新数组保留该值。如果函数返回`false`，那么过滤器会说“滚出去，你这个臭东西”，然后过滤掉。因此得名！我喜欢编程中的事情被很好地命名(▰˘◡˘▰).)

过滤是一个*非常*有用的方法。想想您需要对数据子集执行操作的频率。好了，现在你可以扔掉任何不符合你标准的东西，然后对剩下的东西进行操作。这样既高效又容易理解。我认为这些数组方法拥有的真正力量是它们被链接在一起的能力。

```
const cuisines = [
  { name: 'tacos', rating: 10, status: 'raw' },
  { name: 'thai', rating: 10, status: 'raw' },
  { name: 'pizza', rating: 8, status: 'raw' },
  { name: 'bbq', rating: 7, status: 'raw' },
]

const preppedMeals = cuisines
  .filter(food => food.rating > 8)
  .map(food => ({ ...food, status: 'prepped' })) 
```

Enter fullscreen mode Exit fullscreen mode

我经常使用过滤器来删除数据中包含“未定义”或 null 的值。可以简单地这样做:

```
const safetyDance = array
  .filter(obj => obj.prop) // since undefined and
  // null are falsy they will be removed.
  .map(prop => prop.otherProp) // Safe to do because
// of our filtering! 
```

Enter fullscreen mode Exit fullscreen mode

总而言之,`filter`很像`map`,但不是**转换**数据，而是用于根据您定义的标准过滤数据！当与`map`一起使用时，它变得非常强大，并且它们可以*链接在一起*在一个声明中完成这两个任务！当像这样一起使用这些方法时，你可以开始看到这些方法不仅仅是看起来那么简单。它开始让你想知道如果我们把它提升一个层次会有什么成就。好消息。用 Reduce 你就知道了！订阅以下内容，以便在 reduce 帖子发布时得到通知！