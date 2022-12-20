# 实现堆栈，pt1

> 原文：<https://dev.to/tttaaannnggg/implementing-a-stack-pt1-4djj>

这是一个初学者的问题，最终可能会出乎意料地深奥。这看起来极其简单，但我无法独自获得奖金，所以我正在做一个令人讨厌的彻底的案例研究:

用`push`、`pop`和`getMax`方法实现一个栈。`push`应该将单个项目推到堆栈的顶部，并返回堆栈的长度。`pop`应该移除堆栈中的最后一个项目，并返回移除的项目。`getMax`应该返回堆栈中存储的数字的最大值。

额外收获:getMax 应该在 O(1)时间内计算。

因此，堆栈本身相当简单。我将避免使用数组和原生数组方法，这样我至少可以自己构建它。

下面是构造函数本身的样子:

```
function Stack(){
    this.storage = {},
    this.length = 0
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？我们的堆栈中有 0 个条目，所以我们的长度是 0，我们的存储中什么也没有。

我们希望能够在堆栈中存储东西，所以让我们给我们的`Stack()`构造函数的原型添加一个`push`方法。我们期望我们的`push`在栈顶存储一个条目。这意味着两件事:

1.  我们将只讨论一个论点
2.  我们需要跟踪我们的项目的顺序

我们基本上可以从数组中窃取想法，作为保持项目正确顺序的策略。我们将在我们的`storage`对象上创建与索引相对应的键/值对，如果它是一个数组的话。

幸运的是，我们将知道堆栈中的下一项将存在于我们的`length`的索引处。也就是说，当我们的长度为 0(存储中有 0 个项目)时，我们要添加的下一个项目将是第 0 个索引项目。当我们在存储中有一个项时，我们的长度将是 1，下一个项将是索引为 1 的项。

因此，我们的策略是获取数组的长度，将其作为我们的`storage`中的一个键，并存储我们正在推送的项的值。之后，我们将增加我们的`length`值，使其与我们的存储保持一致。最后，我们将归还我们的`length`，只是为了让它符合规格。

我们的文件将如下所示:

```
function Stack(){
    this.storage = {},
    this.length = 0
}

Stack.prototype.push = function(item){
    this.storage[this.length] = item;
    this.length = this.length + 1;
    return this.length;
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将添加`pop`方法。这将简单地从我们的对象中删除顶部的项目，更新我们的`length`以保持准确，并返回我们删除的项目。

所以我们要这么做:

1.  将我们的`length`减 1
2.  将`length`处的存储值保存到一个单独的变量中，因为它将是我们存储中的最后一项
3.  从我们的存储中删除该项(我们将它保存到一个单独的变量中)
4.  使用我们保存的变量返回保存的项目

这就是:

```
function Stack(){
    this.storage = {},
    this.length = 0
}

Stack.prototype.push = function(item){
    this.storage[this.length] = item;
    this.length = this.length + 1;
    return this.length;
}

Stack.prototype.pop = function(){
    this.length = this.length - 1;
    const output = this.storage[this.length]; //our stored value
    delete this.storage[this.length];
    return output;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后是真正的工作，也就是我们的`getMax`函数。有一个显而易见的方法，就是从我们的`storage`中获取值，并对它们调用 Math.max()。

```
function Stack(){
    this.storage = {},
    this.length = 0
}

Stack.prototype.push = function(item){
    this.storage[this.length] = item;
    this.length = this.length + 1;
    return this.length;
}

Stack.prototype.pop = function(){
    this.length = this.length - 1;
    const output = this.storage[this.length]; //our stored value
    delete this.storage[this.length];
    return output;
}

Stack.prototype.getMax = function(){
    return Math.max(Object.values(this.store));
} 
```

Enter fullscreen mode Exit fullscreen mode

对于获取一个`max`值来说，这是一个不错的解决方案，但是无论如何它都不是完美的。它的运行时间是 O(n ),因为`Math.max()`需要查看我们堆栈中的所有值，然后才能明确知道最大的数字是多少。

所有的乐趣都从第 2 部分开始，在那里我们将实际上巧妙运用我们的`getMax`来操作时间复杂性的圣杯，O(1)时间。

[更新！下面是第二部分](https://dev.to/tttaaannnggg/stack-pt2-o1-max-18ik)