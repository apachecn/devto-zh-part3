# Javascript 袜子商人挑战-解决方案 2

> 原文：<https://dev.to/adyngom/javascript-sock-merchant-challenge-solution-2-2b39>

### 你可以在这里找到第一部分

[![video camera](img/6ce423bda928ca22c2d375147e9ee787.png) 2:15 ](/adyngom/javascript-sock-merchant-challenge-solution-1-4knh) [![adyngom image](img/8af2fd1ea3d990cbd0bcd822ed619254.png)](/adyngom) [## Javascript 袜子商家挑战-解决方案 1

### Ady Ngom ・ Apr 23 '19 ・ 3 min read

#showdev #javascript #beginners #career](/adyngom/javascript-sock-merchant-challenge-solution-1-4knh)

## 挑战

[黑客等级袜子商家页面](https://www.hackerrank.com/challenges/sock-merchant/problem)

约翰在一家服装店工作。他有一大堆袜子，必须按颜色配对出售。给定一个表示每只袜子颜色的整数数组，确定有多少双颜色匹配的袜子。T3】

## 代码

```
function stockAndCount( n, arr ) {
    let pairs = 0;
    const colors = arr.reduce((acc, val) => {
        (!!acc[val]) ? acc[val] += 1 : acc[val] = 1;
        return acc;
    }, {});

    Object.keys(colors).forEach( n => {
        let _pair = parseInt( colors[n] / 2);
        if ( _pair >= 1 ) pairs += _pair;
    });

    return pairs;
} 
```

## 视频抄本

在[第 1 部分](https://dev.to/adyngom/javascript-sock-merchant-challenge-solution-1-4knh)中，我们使用**排序优先和比较方法**解决了挑战，让我们清理一下，看看替代解决方案。

使用 stockAndCount 函数，我们将创建一个对象，它将存储我们的每种颜色作为键。

所以我们仍然会创建一个 pairs 变量，然后这里会有一个 colors 变量，但是在这里使用 reduce 方法，我们将会构建这个对象。

在 reduce 回调函数中，我们设置了一个累加器和当前值——在运行过程中，我们检查当前值是否作为一个键存在于我们的累加器对象中，如果是，我们给它加 1，如果不是，我们创建键并用 1 初始化它。

让我们不要忘记添加空对象作为第二个参数，并在每次迭代后返回累加器

让我们确保该函数已正确关闭

我们上面所做的就是我所说的**字典方法**。

现在我们有了一个每种颜色的对象，让 ket 循环遍历它。

我们遍历每个键并创建一个局部变量对。我们通过将 colors n 键值除以 2 来初始化该对

现在我们可以检查 pair 值是否大于或至少等于 1。如果为真，我们可以用找到的线对数量增加第 17 行的总线对值

然后，我们可以在循环后简单地返回总计数。在终端中运行它又给我们提供了 3 对——如果速度太快，让我们添加到控制台语句中并再次运行它

在上一集里，我们首先使用了排序和计数方法，今天我们来看看字典方法，将每种颜色作为关键字进行存储，并合计配对数。

在下一集也是最后一集，我们将讨论为什么第二种方法更好。与此同时，请让我知道你对每个人的看法，以及为什么在评论中一个可能比另一个更好。

干杯