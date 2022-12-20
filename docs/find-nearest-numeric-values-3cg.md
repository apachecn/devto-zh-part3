# 查找最近的数值

> 原文：<https://dev.to/ptable/find-nearest-numeric-values-3cg>

当我第一次看到函数式编程时，我并不喜欢它。这看起来像是聪明的过度设计，用简单的程序代码替换复杂的一行程序。我已经习惯了不要试图变得聪明，创造临时变量只是为了让未来的我明白发生了什么。

然而，在决定是否进一步优化时，我主要是凭直觉。如果我脑海中的那个小小的声音坚持认为有更好的方法去做某件事，它不会放手，直到我找到它。这个特殊的问题特别适合函数式编程。

我的目标是找到在数字上最接近给定搜索值的一个或多个值。由于可能不止一个，这需要两次通过。第一步是找到搜索值和所有搜索值之间的最小差异。然后，在第二次传递时，每个接近的值都被认为是匹配的。我对着一个`Map()`这样做，并把键按到`matches`上，但是为了简单起见，这里显示的是一个空数组。

```
const haystack = [1, 2, 2, 3, 4, 5, 6];
const search = 2.5;
let matches = []; 
```

```
let closest = Number.MAX_VALUE;
for (const value of haystack) {
    const difference = Math.abs(value - search);
    if (difference < closest)
        closest = difference;
}

for (const value of haystack) {
    const difference = Math.abs(value - search);
    if (difference <= closest)
        matches.push(value);
}

matches;
<- [2, 2, 3] 
```

至少，每个通道本身都可以发挥作用。`Array.reduce()`是寻找最接近值的一个很好的候选，因为它将数组从许多元素减少到一个元素。我们再次传入`Number.MAX_VALUE`作为初始值，以确保远远超出我们范围的搜索仍然创建匹配。每一次连续的传递都使用`Math.min()`来寻找更接近的值。然后，一个简单的`Array.filter()`可以遍历干草堆并返回一个数组，其中包含与数字搜索最接近的值。

```
let closest = haystack.reduce((closest, current) =>
    Math.min(Math.abs(current - search), closest),
    Number.MAX_VALUE
);

matches = haystack.filter(value =>
    Math.abs(value - search) <= closest
);
<- [2, 2, 3] 
```

由于`closest`在第二个块中只出现一次，所以从技术上讲，将第一个块的所有内容都填充到第二个块中，使之成为真正的一行程序是可能的。抵制诱惑，因为这将导致它一遍又一遍地为数组的每个元素寻找最接近的值，除非静态分析已经走了很长的路。