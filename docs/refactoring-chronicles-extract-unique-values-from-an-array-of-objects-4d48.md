# 重构编年史:从对象数组中提取唯一值

> 原文：<https://dev.to/dvddpl/refactoring-chronicles-extract-unique-values-from-an-array-of-objects-4d48>

我今天做的一个代码回顾的快速笔记。
用例从 CSV 文件的所有行中提取所有唯一 id 的列表。

加载 CSV 并解析后，数组看起来如下:

```
const rows = [
  {"id":1,"date":"03.03.2019","otherProps":483},
  {"id":2,"date":"22.02.2019","otherProps":573},
  {"id":3,"date":"11.03.2019","otherProps":645},
  {"id":4,"date":"03.03.2019","otherProps":483},
  {"id":2,"date":"08.03.2019","otherProps":573},
  {"id":4,"date":"26.02.2019","otherProps":645},
  {"id":5,"date":"13.03.2019","otherProps":483},
  {"id":3,"date":"22.01.2019","otherProps":573},
  {"id":1,"date":"01.03.2019","otherProps":645}
]; 
```

Enter fullscreen mode Exit fullscreen mode

拉请求中的实现是这样的:

```
const findUnique = (arr) => {
  return arr.reduce((acc, row) => {
            if (typeof row.id === 'number' && acc.indexOf(row.id) < 0) {
                acc.push(row.id)
            }
            return acc
        }, [])
} 
```

Enter fullscreen mode Exit fullscreen mode

我真的很感激开发人员试图在这里使用 *reduce* ,但是尽管 reduce 很有用也很酷，我发现代码太冗长了。

在我看来，一个更具可读性的解决方案可能是首先只提取 id，以消除混乱，然后过滤第一次出现的 id，忽略重复的 id。

```
const findUnique = (arr) => arr.map(r => r.id).filter((id, i, ar) => ar.indexOf(id) == i) 
```

Enter fullscreen mode Exit fullscreen mode

但是我的建议是这个神奇的 es6 技巧，它将使这个功能成为一行程序

```
const findUnique = (arr) => [ ...new Set(arr.map(r => r.id))] 
```

Enter fullscreen mode Exit fullscreen mode

这是在做什么？
我们正在通过*地图*提取 id，并用结果创建一个集合。
因为如[文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set#Description) **中所述，集合中的一个值只能出现一次；它在该系列中独一无二。所有的重复都会被自动删除。
然后使用**...操作符**我们将集合转换回数组。**

[![tadaa](img/8a22468fec9f61f0b5aa9a60f18153ec.png)](https://i.giphy.com/media/8FfwJr9MnLw7OZBCrw/giphy.gif)

PS:在使用 *map reduce filter* 和其他魔术技巧(比如这里的数组到集合的转换)时要小心，因为使用*非常*大的数组性能可能会受到影响。在这种情况下，最好牺牲可读性和酷，并在一个循环中执行所有必要的操作，这样数组只遍历一次。

PPS:总是对你的方法进行单元测试，这样当你尝试其他解决方案时，你就能确定你的代码仍然能按预期工作。

```
import test from "ava"
test('return list of unique IDs', t => {
    const expected = [1, 2, 3, 4, 5]
    const result = findUnique(rows);
    t.deepEqual(result, expected)
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用这个[代码笔](https://codepen.io/anon/pen/moXYZe?editors=0012)来试验代码和其他解决方案