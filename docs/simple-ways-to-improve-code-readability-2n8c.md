# 提高代码可读性的简单方法

> 原文：<https://dev.to/briwa/simple-ways-to-improve-code-readability-2n8c>

**TL；大卫:这对你来说可能是显而易见的，但对其他人来说不是。**

## 为什么？

我常常认为我的代码只适合我自己(*旁白:它不是*)。无论我是在工作中编码还是为我自己的副业项目编码，总有一天别人会查看代码并试图理解它是如何工作的。你最不希望发生的事情就是他们花太多时间做这件事。

一个小小的免责声明:这不是关于重写你的代码和使用一个完全不同的编程范式(因为 FP 是最好的 P ),也不是关于变量名和注释过于冗长。我会留着下次再说。我发现，即使是我编写代码的方式中最小的变化，实际上也有助于提高可读性。小到足以开个好头。

## 在野外

让我们考虑几个代码示例。

### #1

```
// When the id is in the list, retrieve by id
if (ids.indexOf(id) !== -1) {
  retrieveItemById(id);
} 
```

显然，这段代码是有效的。我编码它，合并它，去吃午饭，然后大部分时间都忘了。然后其他人读了这段代码:

> 如果 id 列表中 id 的索引不等于负一，则按 id 检索项目。

那个人嘀咕道，*好吧，这是一种说法*。

看，这应该是可以避免的。那个人不应该说那种话。无论如何，让我们来看看我应该如何修复它。

诚然，如果值不在列表中，`.indexOf`将返回-1，但这不是我想说的，对吗？我想当 id 在 id 列表中时*做些什么，而不是当 id 不在 id 列表*中时*做些什么。*

这很微妙，但在我看来，这样的代码读起来更好:

```
// When the id is in the list, retrieve by id
if (ids.indexOf(id) >= 0) {
  retrieveItemById(id);
} 
```

见鬼，我可以加入一些 [ES6 魔法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)，可读性现在是 3000 倍:

```
// When the id is in the list, retrieve by id
if (ids.includes(id)) {
  retrieveItemById(id);
} 
```

但是等等，还有呢！

### #2

```
// See if any of the results has an error
const isSuccess = results.filter((result) => result.error === true).length === 0; 
```

这和上一个类似。如果整个考验是结果中是否没有错误，那么它应该是一个返回布尔值的语句。使用`.filter`然后检查数组长度会使阅读和理解代码的时间更长。

```
// See if any of the results has an error
const isSuccess = !results.some((result) => result.error === true) 
```

但是，在语句前面加上否定会使代码不太清楚。

> 当*而不是*某些结果有错误时就是成功。

一个改进的版本是:

```
// See if any of the results has an error
const isSuccess = results.every((result) => result.error !== true) 
```

代码现在如下所示:

> 当每一个结果都没有错误时，这就是成功。

`.some`和`.every`比`.filter`有优势，因为如果前者的语句为真，后者的语句为假，它将停止遍历数组。因此，即使从效率的角度来看，`.some`和`.every`在这种情况下也比`.filter`好。

最后一个，我保证。

### #3

```
// Only ship item that isn't broken and expired
if (!item.broken && !item.expired) {
  shipItem(item);
} 
```

随着时间的推移，我们注意到我们一直在运送没有库存的商品。很好....

```
// Only ship item that isn't broken, expired, and stock is not zero (duh)
if (!item.broken && !item.expired && item.stock !== 0) {
  shipItem(item);
} 
```

我是说，看起来不错，对吧？对吗？*错了*。

同样，我的意思并不是说如果库存不在那里就发货，而是说如果库存在那里就发货，而不是说 T2。

可能是我在遵循其余逻辑(`!item.broken`，`!item.expired`)的模式，下意识地去进行另一个否定，但对于这种情况，最好是在没有否定的情况下阅读它。

```
// Only ship item that isn't broken, expired, and stock is there
if (!item.broken && !item.expired && item.stock > 0) {
  shipItem(item);
} 
```

## 结论

这些例子都是非常简单、容易避免的问题。当然，还有其他不同的方法来提高代码的可读性。我的主要观点是，我们应该努力停止“我的代码可以工作，我理解得很好”的心态。编码本质上不仅仅是让它工作，它还教育其他人它是如何(以及为什么)工作的。

对我来说，一个好的代码就像人们如何描述一个好的 UX 或者一个好的笑话:如果你必须解释它，它可能没有那么好。定义你的代码是否容易理解的是其他人，而不是你。因此，只要有可能，就要重新审视你的代码并加以改进，这样别人就不必这么做了。

当然，这并不意味着你必须降低到一个生物学专业的学生应该理解你的代码的水平。我认为一个好的代码也是在让它易于理解和必须解释一切之间的平衡。这需要经验。

感谢阅读(我的第一篇文章，btw)。

* * *

*封面图片由[尼古拉斯·托马斯](https://medium.com/r/?url=https%3A%2F%2Funsplash.com%2Fphotos%2F3GZi6OpSDcY%3Futm_source%3Dunsplash%26utm_medium%3Dreferral%26utm_content%3DcreditCopyText)在[Unsplash](https://medium.com/r/?url=https%3A%2F%2Funsplash.com%2Fsearch%2Fphotos%2Feye-test%3Futm_source%3Dunsplash%26utm_medium%3Dreferral%26utm_content%3DcreditCopyText)T5 拍摄。*