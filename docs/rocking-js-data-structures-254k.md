# 摇摆的 JS 数据结构！

> 原文：<https://dev.to/areknawo/rocking-js-data-structures-254k>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

JavaScript 的发展一直到 2015 年都相当顽固。是的，那是宣布 ES6 的神奇时刻，整个网络开发真的起飞了，受欢迎程度成倍增长。📊但是，这是每个 JS 爱好者可能都知道的事情——这一年，这个确切的时刻在世界各地的许多 JS 资源中被反复引用。所以，让我们再创新一次，好吗？😅

ES6 给 JS 带来了大量的新东西。不仅现在必须拥有的**箭头函数**、**承诺**和语法糖，还有新的**数据结构**。🔢没错，我说的是集合、武器地图等东西。(如果你已经认识他们的话)。这些小而有趣的特性被推到了幕后，主要是因为现代浏览器花了很长时间才完全接受新的规范。随着时间的流逝⏳人们开始使用新的语法和一些真正想要的新功能，但是这些结构变得不那么相关了。当然不是对所有人，但就拿我自己这样明显的例子来说，我几乎从未使用过它们。我只是坚持使用老式数组和对象，并生活在有限的范围内。但是，不要担心，因为在本文中，我们将探索这些结构到底有多好和有用。他们提供的新的**可能性**和他们当前的支持...为什么不呢？😃

# [t1](#typedarrays)[型距](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)

我猜你知道数组，因为谁不知道呢？它们提供的所有方法、函数式编程的可能性等等都令人印象深刻。但是，如果是这样的话，那么什么是类型阵列，我们为什么需要它们？

TypedArrays 不是有一个单独的类，而是一个用于引用这些特定结构的不同类型的名称。它们基本上是作为自定义的、**类似数组的视图**到**二进制数据缓冲区**，我想这需要更多的解释。😉

## [t1](#arraybuffer)[阵列缓冲器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)

**ArrayBuffer** 是一个用来包含**定长原始二进制数据**的类。💾您可以通过使用带有长度参数的构造函数来创建一个，指示您的缓冲区的**字节数**。

```
const buffer = new ArrayBuffer(8); 
```

Enter fullscreen mode Exit fullscreen mode

ArrayBuffers 本身没有很多属性。最值得注意的是`byteLength`和`slice()`——一个用于检索缓冲区的字节长度(就像提供的那个),另一个用于分割缓冲区的指定部分并创建新的部分。你可以与 ArrayBuffers 交互的唯一方式是通过所谓的**视图**——要么是**类型数组**要么是 **[数据视图](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DataView)** (但那是另外一个故事了)。

数组缓冲区的重要性来自于它们代表你的数据的方式。这种形式是一些底层 API 需要的，比如 **WebGL** ，因为它的**效率**🏎和整合🤝代码的其他部分，例如着色器。

## [类型阵列[s]](#typedarrays)

现在，我们知道 TypedArrays 作为 ArrayBuffer 的视图，让我们首先列出它们！

*   **Int[8/16/32]Array** -用于将缓冲区解释为由**整数**组成的数组，用**给定的位数**来表示每一个；
*   **Uint[8/16/32]数组** - **无符号整数**与**给定的位数**一一对应；
*   **Float[8/16/32/64]Array**-**浮点数**，其中**给定位数**；
*   **BigInt64Array** - **整数** (bigint)各有 **64 位**；
*   **biguint 64 array**-**-**(bigint)个无符号整数，每个有 **64 位**；

以上每种类型的类型数组都有相同的**方法**和**属性**的**集合**，唯一的区别在于表示数据的方式。TypedArray 实例可以用给定的**长度**(内部创建 array buffer)**创建另一个 TypedArray** ，**一个对象**(用给定索引的长度和值作为键)或以前实例化的 **ArrayBuffer** 。👨‍💻

## 用法

现在，您已经准备好了 TypedArray，可以用类似于普通数组的方法自由地编辑它。👍

```
const typedArr = new Uint8Array([0,1,2,3,4]);
const mapped = typedArr.map(num => num * 2); // Uint8Array [0,2,4,6,8] 
```

Enter fullscreen mode Exit fullscreen mode

不过，有一点需要注意，因为在后台，你操作的是数组缓冲区的数据，你的类型数组有固定的大小。此外，可以在普通数组中找到的所有方法，编辑它们的大小(移除、添加、剪切等。)有**有限**的可能性或者完全**不可用**。🤐

```
const typedArr = new Uint8Array([0,1,2,3,4]);
typedArr.push(5) // Error! You must be kidding me! 
```

Enter fullscreen mode Exit fullscreen mode

你也可以在这些数组上迭代，**将**转换成**标准数组**，只要你愿意。

```
const typedArr = new Uint8Array([0,1,2,3,4]);
for(const num of typedArr){
    // code
}
const arr = Array.from(typedArr); // [0,1,2,3,4] 
```

Enter fullscreen mode Exit fullscreen mode

TypedArrays 也提供了某些与其二进制相关的功能！例如，您可以使用`buffer`属性访问底层 ArrayBuffer 实例，并分别使用`byteLength`和`byteOffset`读取其字节长度和偏移量。🙂

## 用例

正如我之前提到的， **ArrayBuffers** 拥有**的巨大潜力**，因为它们表示数据的方式。这种紧凑的形式可以很容易地用在许多许多地方。它可以是例如**矢量**🎨或其他**压缩数据**📦从服务器发送，打包用于所有阶段的**最大速度**和**性能**-压缩、传输和解压缩。此外，正如我前面所说，一些**Web API**很好地利用了这种格式带来的效率。👌

有了位于 ArrayBuffers 之上的 TypedArrays，操作里面的数据就容易多了(肯定比设置位本身要好😅).除了固定大小的唯一限制，您可以像处理日常数组一样处理这种紧凑的数据。

# [集](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)

继续我们对类数组结构的研究，我们将进入**集合**。🗃这些与数组非常相似——它们可以用相似的方式存储数据，只有一个重要的区别。Set 的所有值必须是**唯一的**(有一些[的怪异情况](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set#Value_equality) tho😵)——不管我们说的是**原始值**还是**对象引用**——双精度都是自动移除的。

## 用法

创建集合很容易——您只需要使用带有可选参数的正确的构造函数，从一开始就提供数据。

```
const dataSet = new Set([1, 2, 3, 4, 5]); 
```

Enter fullscreen mode Exit fullscreen mode

集合提供了他们自己的非常有表现力的 API。最重要的方法是:

*   `add()` -将给定值追加到集合的末尾；
*   `delete()` -从集合中删除给定值；
*   `has()` -检查给定值是否存在于集合中；
*   `clear()` -从集合中删除所有值；

它们也可以被转换成标准数组，并随意迭代。

```
const dataSet = new Set([1,2,3]);
const values = [0,1,2,3,4];
for(const value of values) {
    if(dataSet.has(value)){
        dataSet.delete(value)
    } else {
        dataSet.add(value);
    }
}
const result = Array.from(dataSet); // [0,4]; 
```

Enter fullscreen mode Exit fullscreen mode

## 用例

集合的大多数用例显然基于它们只存储**唯一值**的能力。⚡将这种技术用于数组需要一些额外的样板文件。因此，当**存储 id**等时，唯一值会特别有用。🆔

其次，**移除集合中的元素**要方便得多。只提供要删除的值，而不是执行整个查找索引和拼接过程，这样会方便得多。👍当然，对于标准数组允许的重复值，这是不可能的。

# [孬种](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)

现在，让我们来谈谈不同种类的集合。🤨WeakSets 很特殊——它们以不同的方式存储值，但也有一些额外的限制，比如小得多的**API**。

## 记忆

首先，简单介绍一下 WeakSets 如何存储它们的值。**只有对象**可以用作 WeakSets 的值。不允许原语。🛑:这非常重要，因为 weaksets 存储数据的方式是“弱”的。“弱”意味着如果没有对给定对象的其他引用(对象是通过引用访问的)，它们可以在任何时候被垃圾收集和🗑移除。因此，需要很好地理解引用以及对象是如何交互的，以便正确地利用**弱结构**的潜力。

因为弱势群体仍然...集合，它们存储的所有值必须是唯一的。但是，正如您可能知道的，这对于对象来说没什么大不了的——这是 WeakSets 值的唯一可能类型。因为它们都是由存储的**👉**参考**，即使是性质完全相同的物体，也被认为是不同的。**

## 用法

与普通集合相比，弱集合的 API 受到很大限制。可能最重要的事实是它们是**不可迭代的**。它们没有任何属性(集合有例如`size`来表示它们存储的值的数量)，只有 3 个主要的方法- `add()`、`delete()`和`has()`。构造函数方法看起来是一样的，只是可选的数组参数只需要存储对象。然而，使用这样的参数**没有多大意义**，因为你存储的所有对象都需要在你代码的其他地方被引用**。** 

```
const weakDataSet = new WeakSet();
const obj = {a: 10};
weakDataSet.add(obj);
weakDataSet.add({b: 10}); // Pointless - will be removed soon
weakDataSet.has(obj); // True
weakDataSet.has({a: 10}); // False - objects are stored by reference 
```

Enter fullscreen mode Exit fullscreen mode

## 用例

实际上，可能很难找到 WeakSets 的良好用例。那是因为，在现实中，并不多，而且真的很具体。最流行也可能是最好的一种叫做**物体标记**。你可以使用你的 WeakSets 来**分组**，从而**标记**特定的对象，当它们在你的代码中的其他地方被引用的时候。有些人可能喜欢称之为标记或分组，如果使用得当，这可能是一种非常有用的技术。⚠

然而，你需要小心谨慎。记住，所有没有在其他地方被引用的对象都将被**垃圾收集**。但是，这并不意味着它们会立即被删除，而是在垃圾收集器的下一个周期⭕**被删除。你应该记住这个事实，并且**不要太相信弱者——有些价值观迟早会被移除。****

# [地图](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)

我认为，映射是一种结构，可以同时利用数组和对象。在它们内部，所有数据都存储在**键-值**对中。🤝这种方法和普通对象的区别可以在**API**中进一步注意到。此外，在地图中，键和值被同等对待**，这意味着你甚至可以做一些有创意的事情，比如设置一个对象(但是记住你需要一个对它的引用以便以后访问)作为你的值的实际键！此外，与对象不同，存储在地图中的配对有特定的顺序，很容易重复。🔄**

 **## 用法

您可以通过直接调用构造函数来创建地图实例。您可以选择预先提供一组键值数组作为地图的起始值。

```
const map = new Map([["key1", 10], [10, "value2"]]); 
```

Enter fullscreen mode Exit fullscreen mode

当涉及到 API 时，地图才真正大放异彩。它允许您以更快、更易读的方式进行特定的操作。

有一个特殊的属性叫做`size`(也可以在集合中获得)，它可以给你一个关于在给定时刻**键-值对的数量**的快速注释。其特别之处在于，在老式对象中没有类似的、足够简单的方法来做同样的事情。😕

这种直观的 API 的好处还不止于此！如果您已经喜欢集合的 API，您可能会很高兴知道它与地图的 API 有许多相似之处。所有用于编辑映射值的方法都像是对新的**键值模式**、集合方法的修改。出于明显的、与理性思考相关的原因，只有`add()`方法被转换为`set()`。😅除此之外，要更改和访问地图数据，您主要使用键而不是值进行操作。

此外，就像集合和对象一样(当涉及更多类似数组的集合时，它可能不那么相关)，映射提供了 3 种方法来读取特定的数据组:

*   `entries()` -以数组的数组形式返回 Map 的键值对；
*   `values()` -返回一个数组中所有的 Map 值；
*   `keys()` -返回一个数组中所有的 Map 键；

这些方法(尤其是当你在练习函数式编程时)很可能在与 object 交互时被广泛使用，因为没有其他方便的方法。地图根本不应该是这种情况。有了 Maps 的 API 和良好的数据结构，你肯定会觉得你的生活变得更轻松了。🌈

```
const map = new Map([['key', 10], ['key2', 10]])
map.forEach((value,key) => {
    map.delete(key);
    map.set(key, 10);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 用例

如你所见，地图给了你一个很好的标准物体的选择。每当你需要**同时访问**键及其值，并且能够**对它们进行迭代**时，地图可能是你最好的选择。

iterable 和 object-like 形式的完美结合显然可以有很多实现。而且，虽然你可以很容易地用一个普通的物体创造出同样的效果——为什么要这么麻烦呢？🤔这个**卓越的 API** 背后的便利性以及它是行业标准的事实使得地图成为许多不同情况下的好选择。👍

# [孬种](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)

WeakMaps 是我们遇到的第二个弱结构。许多来自 WeakSets 的事实在这里也适用！这包括存储数据的方式、**仅对象**规则、**有限 API** 和**无迭代**(没有方法给你这些弱存储键的列表)。

如您所知，地图(以及 WeakMaps)将数据存储在**键值模式**中。这意味着在这个结构中实际上有两个数据集合——键和值。WeakMaps 的*【弱】*部分只适用于**键**，因为正是它们负责允许我们访问值。提到的值以正常方式存储或者如果你喜欢这个名字，**强方式**。💪因此，尽管感觉很奇怪，在 WeakMaps 中，只有对象可以用作有效的键。

## 用法

就像 WeakSets 一样，WeakMaps API 也受到严重限制。您可以使用的所有方法有`get()`、`set()`、`delete()`和`has()`。再次，**没有迭代**。😭但是，如果您考虑可能的用例以及这样的结构如何工作，您将开始更好地理解这些限制。你不能迭代弱存储的东西。你需要引用你的键，所以这 4 个基本方法是最好的方法。等等。等。😏

当然，构造函数接受额外的，但是不太有用的参数来初始化数据。

```
const weakMap = new WeakMap();
const value = {a: 10}
weakMap.set({}, value); /* Key will be garbage-collected, but value
                           will still be accessible through variable. */
weakMap.set(value, 10) // Values don't have to be object-only 
```

Enter fullscreen mode Exit fullscreen mode

## 用例

WeakMaps 与 WeakSets 有相似的用例- **标记**。所有这些事情都发生在钥匙边上。然而，作为**强存储的**不同类型**的**数据的值不必与特定的键一起被垃圾收集。如果早一点保存到变量中，仍然可以自由使用。这意味着您不仅可以标记数据的一个方面(键),还可以标记数据的另一个方面(值),这取决于两者之间的关系。🙌

# 就这些？

暂时-是的。🤯我希望这篇文章能帮助你学到一些新东西，或者至少提醒你一些基本知识。你的 JS 代码不必仅仅依赖于对象和数组，尤其是随着现代浏览器占据越来越多的市场份额。📊此外，除了弱结构和它们的内部行为，上面所有的结构都有非常简单和漂亮的 **polyfill 选项**。这样，你就可以自由地使用它们，哪怕只是为了它们的精细 API。

那么，你觉得这个帖子怎么样？在下面与**一个反应**或**一个评论**分享你的观点。它真的帮助我写出更好的文章——你知道，你喜欢读的那些！😀哦，**分享文章**本身，以便更好地触及！还有，**在 Twitter 上关注我****🐦，或在我的脸书页面 上 [**并查看**](https://www.facebook.com/areknawoblog) **[**我的个人博客**](https://areknawo.com) 以了解**该博客的最新内容**。再次感谢你阅读我的内容，希望我能在下一篇文章中看到你！✌****

 **# 资源

*   [**ECMAScript 6**](http://2ality.com/2015/09/typed-arrays.html)中的类型化数组来自*【2 ality . com】*；
*   [**来自*【JavaScript . info】*的 Map、Set、WeakMap 和 weak Set**；](https://javascript.info/map-set-weakmap-weakset)
*   [**ES6 集合:使用来自*【site point . com】*的 Map、Set、WeakMap、weak Set**](https://www.sitepoint.com/es6-collections-map-set-weakmap-weakset/)；****