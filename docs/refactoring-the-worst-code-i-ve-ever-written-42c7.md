# 重构我写过的最糟糕的代码

> 原文：<https://dev.to/jnschrag/refactoring-the-worst-code-i-ve-ever-written-42c7>

在最近关于“开发人员自白”的聊天中，我承认当我 3 年前开始我的第一份开发工作时，我真的不知道我在做什么。为了证明我缺乏经验，我分享了一个我当时编写的代码的例子。

> ![unknown tweet media content](img/4572ef63463b3e2b518e80b6c1f11b1e.png)![Jacque Schrag profile image](img/1d0d6aac0e9cb04cc70f6b8cbf46868a.png)Jacque Schrag[@ jnschrag](https://dev.to/jnschrag)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)自白:刚开始工作的时候完全是临场发挥。这是我写的最喜欢的代码怪物之一。😂我保证我现在知道怎么用循环了。[# dev discuse](https://twitter.com/hashtag/DevDiscuss)01:13AM-03 2019 年 4 月[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1113248130600927232)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1113248130600927232)34[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1113248130600927232)548

我得到的回应是非常积极的。我们中的大多数人都写过不值得骄傲的“糟糕”代码，但当你回头看那些旧代码时，这是一种成长的迹象，你会意识到它还可以做得更好，也许还会因为你所做的选择而嘲笑自己。本着继续学习的精神，今天我想分享一些我可能解决这个问题的方法。

* *尽管这段代码很傻，本来可以写得更有效率，但硬编码可以很好地完成任务。*

## 语境&目标

在重构任何遗留代码之前，后退一步并评估编写代码的上下文是至关重要的。开发人员做出的~~疯狂~~的选择可能有一个重要的原因，它受到你可能没有意识到的(或者记住，如果这是你的代码)上下文的影响。就我而言，我只是经验不足，所以可以安全地重构这段代码。

代码是为两种数据可视化而编写的:[“全球外国直接投资股票”](https://chinapower.csis.org/china-foreign-direct-investment/#global-foreign-direct-investment-stocks)(流入/流出)和[“中国双边投资流出”](https://chinapower.csis.org/china-foreign-direct-investment/#china-bilateral-investment-outflows)(中国)。它们有类似的数据&功能，主要目标是允许用户通过按类型、年份或地区过滤来探索数据集。我将把重点放在全球数据上，但中国的数据集可以用类似的方式重构。

[![An interactive bubble chart showing global direct investment.](img/e47e119132276ba7cfd3ab176de0e7a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a-OXR-xT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ws8aap5cnq8i1pmauqbg.png)

让我们假设更改其中一个过滤器将导致返回以下值:

```
 let currentType = 'in' // or 'out'
    let currentYear = 2017
    let currentRegions = ['Africa', 'Americas', 'Asia', 'Europe', 'Oceania'] 
```

*注意:*region 复选框目前不这样工作，因此代码片段中有“All”和“Partial ”,但这是它应该做的。

最后，这里有一个从 CSV 加载数据后的简化示例:

```
 const data = [
      { country: "Name", type: "in", value: 100, region: "Asia", year: 2000 },
      { country: "Name", type: "out", value: 200, region: "Asia", year: 2000 },
      ...
    ]
    // Total Items in Array: ~2,400 
```

## 选项 1:初始化空对象

除了被硬编码之外，我最初的代码片段完全违反了不要重复自己(DRY)的代码编写方法。在某些情况下，重复自己是有意义的，但是在这种情况下，当相同的属性被一遍又一遍地重复时，动态地创建对象是更明智的选择。这样做还可以减少向数据集中添加新年度时所需的人工工作量，并减少输入错误的机会。

有几种不同的方法可以让它变得更干燥:`for`、`.forEach`、`.reduce`等等。我将使用`.reduce` Array 方法，因为它处理一个数组并将其转换成其他东西(在我们的例子中，是一个对象)。我们将使用`.reduce`三次，每次分类一次。

让我们从声明我们的类别为常量开始。将来，我们只需要在我们的`years`数组中添加一个新的年份。我们将要编写的代码会处理剩下的事情。

```
 const types = ['in', 'out']
    const years = [2000, 2005, 2010, 2015, 2016, 2017]
    const regions = ['Africa', 'Americas', 'Asia', 'Europe', 'Oceania'] 
```

与其将此视为类型→年份→区域，不如反过来从区域开始。一旦`regions`变成一个对象，该对象将是分配给 years 属性的值。这同样适用于多年来的各种类型。注意，可以用更少的代码行来写这个，但是我选择清晰而不是聪明。

```
 const types = ['in', 'out']
    const years = [2000, 2005, 2010, 2015, 2016, 2017]
    const regions = ['Africa', 'Americas', 'Asia', 'Europe', 'Oceania']

    /*
      Convert regions to an object with each region as a property and 
      the region's value as an empty array.
    */
    const regionsObj = regions.reduce((acc, region) => {
      acc[region] = []
      return acc
    }, {}) // The initial value of the accumulator (`acc`) is set to `{}`. 

    console.log(regionsObj)
    // {Africa: [], Americas: [], Asia: [], Europe: [], Oceania: []} 
```

现在我们有了 regions 对象，我们可以对年份和类型做类似的事情。但是我们没有像对区域那样将它们的值设置为空数组，而是将它们的值设置为前一个类别的对象。

编辑:我注意到，当您试图加载数据时，原来的代码片段实际上不起作用了，因为我只是引用了一个现有的对象，而不是实例化一个新的对象。以下代码片段已更新，通过创建现有对象的深层副本来解决此问题。在这篇关于[“如何区分 JavaScript 中的深层和浅层拷贝”](https://medium.freecodecamp.org/copying-stuff-in-javascript-how-to-differentiate-between-deep-and-shallow-copies-b6d8c1ef09cd)的文章中有一个解释，作者是 Lukas Gisder-Dubé。

```
 function copyObj(obj) {
      return JSON.parse(JSON.stringify(obj))
    }

    /* 
      Do the same thing with the years, but set the value 
      for each year to the regions object.
    */
    const yearsObj = years.reduce((acc, year) => {
        acc[year] = copyObj(regionsObj)
      return acc
    }, {})

    // One more time for the type. This will return our final object.
    const dataset = types.reduce((acc, type) => {
      acc[type] = copyObj(yearsObj)
      return acc
    }, {})

    console.log(dataset)
    // {
    //  in: {2000: {Africa: [], Americas: [],...}, ...},
    //  out: {2000: {Africa: [], Americas: [], ...}, ...}
    // } 
```

我们现在得到了与我最初的代码片段相同的结果，但是成功地重构了现有的代码片段，使其更具可读性和可维护性！在向数据集中添加新的一年时，不再需要复制和粘贴！

但问题是:这种方法仍然需要有人手动更新年份列表。如果我们无论如何都要将数据加载到对象中，没有理由单独初始化一个空对象。接下来的两个重构选项完全删除了我的原始代码片段，并演示了如何直接使用数据。

*先不说:老实说，如果我在 3 年前尝试编写这个代码，我可能会做 3 个嵌套的`for`循环，并对结果感到满意。但是嵌套循环会对性能产生显著的负面影响。这种方法分别关注分类的每一层，消除了额外的循环并提高了性能。编辑:查看[这个评论](https://dev.to/stecman/comment/a9oj)中关于这个方法的例子和关于性能的讨论。*

## 选项 2:直接过滤

你们中的一些人可能想知道为什么我们还要麻烦地将我们的数据按类别分组。基于我们的数据结构，我们可以使用`.filter`根据`currentType`、`currentYear`和`currentRegion`返回我们需要的数据，比如:

```
 /*
      `.filter` will create a new array with all elements that return true
      if they are of the `currentType` and `currentYear`

      `.includes` returns true or false based on if `currentRegions`
      includes the entry's region
    */
    let currentData = data.filter(d => d.type === currentType && 
    d.year === currentYear && currentRegion.includes(d.region)) 
```

虽然这个一行程序运行得很好，但我不建议在我们的例子中使用它，原因有二:

1.  每当用户做出选择时，这个方法就会运行。根据数据集的大小(记住，它每年都在增长)，可能会对性能产生负面影响。现代浏览器是高效的，性能影响可能微乎其微，但如果我们已经知道用户一次只能选择 1 种类型和 1 年，我们可以通过从一开始就对数据进行分组来主动提高性能。
2.  这个选项没有给出可用类型、年份或地区的列表。如果我们有这些列表，我们可以使用它们来动态生成选择 UI，而不是手动创建(和更新)它。

<figure>

[![A year dropdown with hard-coded options.](img/bcd5d59cbcae95a60853b32f89cfda29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PjQGXOIM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3cyti5affj7n7rkyff86.png)

<figcaption>Yep, I hard-coded the selectors too. Every time we add a new year, I have to remember to update both the JS and the HTML.</figcaption>

</figure>

## 选项 3:数据驱动对象

我们可以结合第一个和第二个选项，以第三种方式重构代码。目标是在更新数据集时根本不必更改代码，而是从数据本身确定类别。

同样，有多种技术方法可以实现这一点，但是我将坚持使用`.reduce`,因为我们将把我们的数据数组转换成一个对象。

```
 const dataset = data.reduce((acc, curr) => {
        /*
          If the current type exists as a property of our accumulator,
          set it equal to itself. Otherwise, set it equal to an empty object.
        */
        acc[curr.type] = acc[curr.type] || {}
        // Treat the year layer the same way
        acc[curr.type][curr.year] = acc[curr.type][curr.year] || []
        acc[curr.type][curr.year].push(curr)
        return acc
    }, {}) 
```

请注意，我已经从 dataset 对象中删除了分类的 region 层。因为与类型和年份不同，可以任意组合一次选择多个区域。这使得预先分组到区域中几乎没有用，因为我们无论如何都必须将它们合并在一起。

考虑到这一点，下面是更新后的一行程序，用于根据所选的类型、年份和地区获取`currentData`。由于我们将查找限制在具有当前类型和年份的数据，我们知道数组中的最大项数是国家的数量(小于 200)，这比选项#2 的`.filter`实现要高效得多。

```
 let currentData = dataset[currentType][currentYear].filter(d => currentRegions.includes(d.region)) 
```

最后一步是获取不同类型、年份和地区的数组。为此，我喜欢使用`.map`和集合。下面的例子展示了如何获得一个包含数据中所有唯一的*T2 区域的数组。* 

```
 /*
      `.map` will extract the specified object property 
      value (eg. regions) into a new array
    */
    let regions = data.map(d => d.region)

    /*
        By definition, a value in a Set must be unique.
        Duplicate values are excluded. 
    */
    regions = new Set(regions)

    // Array.from creates a new array from the Set
    regions = Array.from(regions)

    // One-line version
    regions = Array.from(new Set(data.map(d => d.region)))

    // or using the spread operator
    regions = [...new Set(data.map(d => d.region))] 
```

对 type & year 重复上述操作，创建这些数组。然后，您可以基于数组值动态创建过滤 UI。

### 最终重构代码

将所有这些放在一起，我们最终得到的代码能够适应未来数据集中的变化。不需要手动更新！

```
 // Unique Types, Years, and Regions
    const types = Array.from(new Set(data.map(d => d.type)))
    const years = Array.from(new Set(data.map(d => d.year)))
    const regions = Array.from(new Set(data.map(d => d.region)))

    // Group data according to type and year
    const dataset = data.reduce((acc, curr) => {
        acc[curr.type] = acc[curr.type] || {}
        acc[curr.type][curr.year] = acc[curr.type][curr.year] || []
        acc[curr.type][curr.year].push(curr)
        return acc
    }, {})

    // Update current dataset based on selection
    let currentData = dataset[currentType][currentYear].filter(d => currentRegions.includes(d.region)) 
```

## 最后的想法

清理语法只是重构的一小部分，但通常“重构代码”实际上意味着重新构思不同部分之间的实现或关系。重构很难，因为有几种方法可以解决问题。一旦你想出了一个可行的解决方案，就很难再想出不同的了。确定哪种解决方案更好并不总是显而易见的，并且会根据代码上下文和个人偏好而变化。

我对提高重构能力的建议很简单:多读代码。如果你在一个团队中，积极参与代码评审。如果你被要求重构某个东西，问*为什么*并且试着理解其他人是如何处理问题的。如果你是一个人工作(当我刚开始工作的时候)，注意当不同的解决方案被提供给同一个问题时，寻找最佳代码实践的指南。我强烈推荐阅读[杰森·麦克雷里](https://dev.to/gonedark)的[基础代码](https://basecodefieldguide.com/)。这是编写不太复杂但可读性更好的代码的优秀指南，并且涵盖了许多真实世界的例子。

最重要的是，承认你有时会写出糟糕的代码，经历重构的过程——让它变得更好——是成长的标志，应该庆祝。