# JavaScript 比较:对象与地图

> 原文：<https://dev.to/iaziz786/javascript-comparison-object-vs-map-3m92>

### 出发前

你可能已经从这篇文章的质量猜到了，**这是我的第一篇技术博客文章**。因为我一直想写更多，但没有勇气去面对像你这样的开发者。现在，我少了一点恐惧，多了一点勇气，开始了我成为作家的旅程，请原谅我的错误；我知道有很多。

### 简介

从 JavaScript 诞生之日起，它就存在了。我们已经用了很多次了，将来我们会继续疯狂地使用它们。但是现在，我想让你有点好奇，问自己一个问题，为什么 ECMAScript 委员会引入了这个新的`Map`?在本帖中，我将尝试回答**何时我们应该使用`Map`而不是`Object`** ？

### 什么是`Map`物体？

根据 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) :

> **`Map`** 对象保存键值对，并记住键的原始插入顺序。

### `Map`有什么“特别”之处？

关于`Map`物体有很多特别的东西，但我将只谈论与这篇文章相关的，更重要的是，**它们与`Object`** 有什么不同？

*   与键只能是字符串的`Object`不同， **`Map`对象可以有任意类型的键**。
*   `Map`中的**键是有序的**，而对象中的键不是。这意味着**在迭代`Map`对象的**时，我们将**按照添加键的顺序**获取键。
*   对象的实例有一个名为`size` 的**键，顾名思义，它**给出了地图对象**的大小。你也可以通过做`Object.keys(object).length`来为对象做同样的事情，但是这看起来像一个临时的解决方案。**
*   因为 map 对象是**可迭代的，所以迭代它们是非常容易的**,然而要迭代一个对象的属性，我们必须首先获得所有的键，然后迭代这个键以获得与那个键相关的值。
*   在需要频繁添加和移除键的场景中，`Map`对象可能表现得更好。

### 什么时候用`Map`什么时候用`Object`？

引用 MDN 文档中关于[键控集合](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Keyed_collections#Object_and_Map_compared)的要点:

> *   Use mapping on object **when the key is unknown before runtime, and when all keys are of the same type and all values are of the same type.**
> *   If there is a **that needs to store the original value as the key** , use mapping, because the object treats each key as a string, whether it is a numeric value, a Boolean value or any other original value.
> *   Use objects when there is logic to operate on a single element.

非常感谢您阅读我在技术博客上的第一次尝试。**我成长的一个途径就是通过你的反馈**所以，请花你宝贵的时间以讨论的形式分享你的想法。

### 相关资源:

[键控集合](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
[地图](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Keyed_collections)
[对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)