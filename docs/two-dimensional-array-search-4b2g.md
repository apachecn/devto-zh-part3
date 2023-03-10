# 二维数组搜索

> 原文：<https://dev.to/ptable/two-dimensional-array-search-4b2g>

一个朋友提出了一个难题。在 JavaScript 中搜索二维数组中的元素并返回其包含数组的索引的最干净的方法是什么？

也就是说，如果要求在下面搜索`8`，则返回`2`。

```
const haystack = [
 [1,2,3],
 [4,5,6],
 [7,8,9]
];
const search = 8; 
```

我第一次尝试。

```
haystack.indexOf(haystack.find(arr => arr.includes(search)));
<- 2 
```

然后，在得知`findIndex`之后。

```
haystack.findIndex(arr => arr.includes(search));
<- 2 
```

如果您需要二维数组中的列和行，该怎么办？以下内容重复了部分搜索，因此不是最佳选择。

```
const row = haystack.findIndex(row => row.includes(search));
const col = haystack[row].indexOf(search); 
```

也许`findIndex`内部的函数体可以使用`indexOf`来代替，并被骗去存储它的值，同时仍然返回`true`或`false`以避免破坏外部的`findIndex`，尽管在这一点上它仍然是函数式编程，并且它是否值得使用传统的循环？