# 如何在普通 JavaScript 中移动数组位置

> 原文：<https://dev.to/kmelve/how-to-shift-array-position-in-vanilla-javascript-59oa>

昨天，我在帮助一个用户做一些内容迁移，他们想把数组的最后一个元素放在第一个位置。每次我遇到这样的问题，我都想看看我是否能不去任何图书馆而设法解决它。这是因为我不想给有问题的人带来更多的依赖，但最重要的是，这是一个锻炼心智的机会。

那么，如何像这样改变一个数组:

```
["Cersei", "Joffrey", "Ilyn", "The Mountain", "The Hound", "Melisandre", "Beric", "Thoros", "Tywin", "Meryn", "Walder"] 
```

对这个？

```
["Walder", "Cersei", "Joffrey", "Ilyn", "The Mountain", "The Hound", "Melisandre", "Beric", "Thoros", "Tywin", "Meryn"] 
```

我的第一反应是创建一个新的数组，只针对列表长度减 1 的最后一个元素，然后使用`splice`扩展数组的其余部分。按下绿色的*运行*按钮运行代码:

`const theList = ["Cersei", "Joffrey", "Ilyn", "The Mountain", "The Hound", "Melisandre", "Beric", "Thoros", "Tywin", "Meryn", "Walder"] const newList = [theList[theList.length - 1], ...theList.splice(0, theList.length - 1)] console.log(newList)`

你们中的一些人可能已经猜到为什么这是一个坏主意，你们中的一些人可能会像我一样，总是混淆`splice`和`slice`。让我们看看当*运行*上面的代码时`theList`会发生什么:

`const theList = ["Cersei", "Joffrey", "Ilyn", "The Mountain", "The Hound", "Melisandre", "Beric", "Thoros", "Tywin", "Meryn", "Walder"] const newList = [theList[theList.length - 1], ...theList.splice(0, theList.length - 1)]` `// check output console.log(newList) // check output console.log(theList)`

不理想(除非你是艾莉亚)。用`slice`替换`splice`保持了原始数组的完整性，并且不会对其进行变异(这会导致大量的问题):

`const theList = ["Cersei", "Joffrey", "Ilyn", "The Mountain", "The Hound", "Melisandre", "Beric", "Thoros", "Tywin", "Meryn", "Walder"]` `const newList = [theList[theList.length - 1], ...theList.slice(0, theList.length - 1)] console.log(newList) console.log(theList)`

这正是我们想要的。

另一种方法是使用`Array.prototype.reduce`-方法，在我看来这种方法更干净。还有，`.reduce`值得投入时间学习。

`const theList = ["Cersei", "Joffrey", "Ilyn", "The Mountain", "The Hound", "Melisandre", "Beric", "Thoros", "Tywin", "Meryn", "Walder"]` `const newList = theList.reduce( (accumulatedArray, currentItem, index, originalArray) => index < originalArray.length - 1 ? [...accumulatedArray, currentItem] : [currentItem, ...accumulatedArray], [] ) console.log(newList) console.log(theList)`

这当然可以做成一个函数。虽然很诱人，但我不建议为此使用*匿名*箭头(`=>`)函数，因为如果发生错误，它不会在控制台堆栈跟踪中显示名称:

`const theList = ["Cersei", "Joffrey", "Ilyn", "The Mountain", "The Hound", "Melisandre", "Beric", "Thoros", "Tywin", "Meryn", "Walder"]` `function shiftLastToFirst (theArray) { return theArray.reduce( (accumulatedArray, currentItem, index, originalArray) => { return index < originalArray.length - 1 ? [...accumulatedArray, currentItem] : [currentItem, ...accumulatedArray] }, [] ) } shiftLastToFirst(theList)`

现在，我确信有很多更聪明的方法可以做到这一点。我写下这些并放在这里的原因是我想了解他们。

你将如何着手解决这个问题？