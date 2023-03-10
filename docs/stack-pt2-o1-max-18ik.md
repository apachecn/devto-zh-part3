# 堆栈 pt2: O(1)最大值

> 原文：<https://dev.to/tttaaannnggg/stack-pt2-o1-max-18ik>

[此处第 1 部分](https://dev.to/tttaaannnggg/implementing-a-stack-pt1-4djj)

在我们上次停止的地方，我们有一个带有`push`、`pop`和`getMax`方法的`Stack`构造函数。为了我自己的理智，我将重写代码，以便我们使用数组作为存储，并且可以只使用原生数组方法。

```
function Stack(){
    this.storage = [],
}

Stack.prototype.push = function(item){
    return this.storage.push(item)
}

Stack.prototype.pop = function(){
    return this.storage.pop()
}

Stack.prototype.getMax = function(){
    return Math.max(this.storage)
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，`Math.max()`的时间复杂度为 O(n)。在最坏的情况下，它必须迭代我们在`storage`上得到的所有值，以查看最高值是多少。我们可以做得比这更好。

事实上，我们可以达到时间复杂性的圣杯，O(1)。

我不得不从别人那里学习解决方案，但在我告诉你们之前，我会列出我的一些(失败的)策略。

首先，我认为我们可以保持`Stack`的最大值。不会太难吧？只需创建一个名为`max`之类的新属性，每当我们`push`一个更高的值时，就更新这个值。

假设我们得到的所有值在到达`storage`之前都经过了`push`，我们应该能够做一些工作来跟踪我们的`max`是什么，对吗？

```
function Stack(){
    this.storage = [],
    this.max = -Infinity
}

Stack.prototype.push = function(item){
    if (item > this.max) this.max = item;
    return this.storage.push(item)
}

Stack.prototype.pop = function(){
    return this.storage.pop()
}

Stack.prototype.getMax = function(){
    return this.max
} 
```

Enter fullscreen mode Exit fullscreen mode

太棒了！...算是吧。

假设我们想将数字 3、7 和 9 放到堆栈中。我们将有一个看起来像这样的存储:`['0': 7, '1':3, '2':9]`，以及最大的`9`。好到此为止，不过咱们的`pop`。

现在我们的存储看起来是这样的:`['0': 7, '1':3,]`，但是我们的 max 还是`9`！不好！

所以，我们可能需要在我们的`pop`上有一些东西，当我们弹出当前最高的
时，它将更新`max`值

```
function Stack(){
    this.storage = [],
    this.max = -Infinity
}

Stack.prototype.push = function(item){
    if (item > this.max) this.max = item;
    return this.storage.push(item)
}

Stack.prototype.pop = function(){
    const output = this.storage.pop();
    if (output === this.max) this.max = Math.max(this.storage)
    return this.storage.pop()
}

Stack.prototype.getMax = function(){
    return this.max
} 
```

Enter fullscreen mode Exit fullscreen mode

这...在技术上是一个解决方案，因为 getMax 是一个 O(1)运算，但你知道为什么它不算，对吗？

最终，我们仍然调用`Math.max`来维护堆栈的`max`值。我们这样做只是在定义`pop`。这肯定比每次我们需要得到我们的`max`时都调用`Math.max`要少，但是在我们最坏的情况下，这仍然比 O(1)多。

所以，让我们更深入地思考这个问题。如果我们的`max`不能再有现在的价值，那它应该有什么价值？

它应该有它的*先前值*。好吧，那我们怎么得到它？答案可能会让你大吃一惊。

```
function Stack(){
    this.storage = [],
    this.max = [-Infinity]
}

Stack.prototype.push = function(item){
    if (item >= this.max) this.max.push(item);
    return this.storage.push(item)
}

Stack.prototype.pop = function(){
    const output = this.storage.pop();
    if (output === this.max[this.max.length-1]) this.max.pop()
    return this.storage.pop()
}

Stack.prototype.getMax = function(){
    return this.max[this.max.length-1]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在看起来似乎很简单，但是我们可以为我们的`max`维护“历史”的方法是创建一个*第二个*堆栈。`push`和`pop`在 O(1)时间内操作，所以时间复杂度不是问题，特别是因为我们在堆栈`push`和`pop`方法内处理它们。

让我们看一个例子。如果按顺序推`3, 1, 7, 21, -5, 8`，我们的`storage`会是这样的:`[3, 1, 7, 21, -5, 8]`，我们的`max`会是这样的:`[3, 7, 21]`3

现在，让我们从我们的 T1 中减去一个值。

如果我们`pop`，我们的`storage`将会是`[3, 1, 7, 21, -5]`。我们弹出了`8`，它和我们`max`栈中的最后一个值不一样，所以`max`栈会不变:`[3,7,21]`。

让我们再来看几个值:

`storage: [3, 1, 7, 21]`(弹出-5)，`max: [3, 7, 21]`。`21`是我们`max`的最后一个项目，代表我们堆栈中的最高值。

`pop`又来了:

`storage: [3, 1, 7]`(蹦出 21)。

在这里，我们看到我们的`21`与堆栈的最后一项相同，所以我们将它从`max`中弹出，我们的 max 看起来像这样:`max: [3, 7]`。

...这就对了。

一旦你掌握了诀窍，这就完全简单了，但是改变你概念化你的`max`值的方式可能是一个挑战，特别是因为它在你的栈本身中使用了栈的结构，但是这正是它酷的地方！