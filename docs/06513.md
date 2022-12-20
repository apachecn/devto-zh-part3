# 发现数组 API 好东西！

> 原文：<https://dev.to/areknawo/discovering-array-api-goodies-1m26>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

毫无疑问， [**数组**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) 是最有用也是最流行的 JS 数据结构之一。这些**索引集合**一次又一次地证明了它们仍然是各种用例的最佳(如果不是唯一的)选择。但是，如果没有完整的、极具表现力的 **API** ，数组本身就不会这么有价值。这就是今天帖子的主题！😀

我们将回顾一些不太为人所知的和其他可能很难记住的方法，这些方法**包含在 arrays API 的本地**中。有些可以追溯到惊人的多！在他们的帮助下，你将能够写出更干净、更**功能性**(在 [**FP 风格**](https://en.wikipedia.org/wiki/Functional_programming) 中也是)甚至有时更高性能的 JS 代码！有些可能更容易，有些更难，但这篇文章绝对不是针对完全的初学者！当然，如果你已经知道并记住了所有这些，考虑至少花点时间提醒自己一些细节或者学习一些**新招**！✨

# 转换

为了更好的阅读体验，我将所有列出的方法分成了 4 个独立的组。每一个都收集了共享至少一个特殊属性的方法。第一组叫做*【变身】*。在这里，所有将数组转换成其他形式的方法都找到了自己的位置。它们都以**不可变**的方式工作，在结果中返回新数组，而不影响基数组。

## [。过滤器()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)

我想我不必谈论**过滤**数组有多有用。而且，有了`.filter()`你就可以做到这一点！你所要做的就是传递一个**过滤函数**，在给定当前**元素的值**、**其索引**和**源数组**的情况下，该函数应该输出布尔值，指示给定元素是否应该包含在结果数组中。📋

```
const arr = [1,2,3,4];
const evenArr = arr.filter(num => num % 2 === 0); // [2,4] 
```

Enter fullscreen mode Exit fullscreen mode

## [。地图()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

`.map()`可能是 **FP** 中最受欢迎的数组方法之一——量身定制的 JS 编程。您可能已经知道，它处理(*“maps”*)您的数组，并使用给定的**映射函数**，返回一个包含新处理数据的新数组。上面提到的函数提供了一个元素的**标准参数集**，它的索引和源数组参数，应该会返回包含在结果中的值。因此，有了所有这些数据，您应该拥有了按您想要的方式更改阵列所需的一切！😁

```
const arr = [1,2,3,4];
const oneMoreArr = arr.map(num => num + 1); // [2,3,4,5] 
```

Enter fullscreen mode Exit fullscreen mode

## [。【地图】()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat)

**嵌套数组**在现代相当普遍。事实证明，它们在表示 2D 或 3D 数据格式时特别有用。完全有可能更深入地了解这些维度，但是，众所周知，以后跟踪和访问这些数据只会变得越来越困难。ECMAScript 规范开发的幕后人员显然[认识到了这种模式](https://github.com/tc39/proposal-flatMap)，并且从**最新的 ES 规范**和**永远绿色的浏览器**开始，向我们介绍了新的`.flat()`方法。它的规则很简单——它只是简单地将嵌套的数组展平指定的深度(默认为 1 ),有效地为您留下一个比以前更展平的数组！

```
const arr = [1,[2,[3,[4]]]];
const flatten = arr.flat(3); // [1,2,3,4] 
```

Enter fullscreen mode Exit fullscreen mode

还有另一种与数组展平相关的方法。我说的是 [`.flatMap()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap) ，正如你所料，这是`.map()`和`.flat()`的完美结合。基本上，你可以像`.map()`一样使用这种方法——使用相同的参数集等等。，但是得到的数组稍后被展平 **1 级深**。简单。那么，这种方法有哪些可能的用例呢？为此，请考虑下面的字符串处理示例。

```
let arr = ["This is", "an", "example"];

const mappedArr = arr.map(x => x.split("  ")); 
// [["This","is"],"an","example"]

const flatMappedArr = arr.flatMap(x => x.split("  "));
// ["This","is","an","example"] 
```

Enter fullscreen mode Exit fullscreen mode

也许这有点粗略，但我想你明白了。一旦你理解了这个方法是如何工作的，你肯定会找到一些你自己的用例。作为一个旁注，这种方式比单独使用`.map()`和`.flat()`更有**性能** ⚡。

# 互动

*【交互】*类别将**在源数组**上工作的所有方法分组，并且，不是提供一个全新的方法，而是改变它们或者返回完全**不同类型的值**。🤯

## [。反向()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)

当然简单，但有点鲜为人知的方法，`.reverse()`正如它的名字所暗示的那样- **反转数组中元素的排序**。➰:所以最后的将是第一个。当处理不同种类的队列时，这很可能会派上用场。记住这个方法会改变源数组。

```
const arr = [1,2,3,4];
arr.reverse(); // [4,3,2,1] 
```

Enter fullscreen mode Exit fullscreen mode

## [。减少【右】()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

如果你想快速转换( *"reduce"* )你的数组为一个单一的值，你可以自由地用`.reduce()`方法来做。如果提供了正确的函数(所谓的**缩减器**，它稍后将对数组中的每个元素执行该函数，然后**将结果**累积到一个变量中。

```
const arr = [1,2,3,4];
const sum = arr.reduce((sum, num) => sum + num); // 10 
```

Enter fullscreen mode Exit fullscreen mode

argument 函数应该返回累计值，以后可以用它的第一个参数引用该值。在其峰值，该函数可以为您提供 **4 个参数**(按给定顺序):

*   **累计值**(可以是字符串、数组、对象或任何东西...);
*   数组的**当前值**被减少；
*   **当前值的指标**；
*   **数组**被缩小；

如果降阶对你很重要，你应该知道你还访问了 [`.reduceRight()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight) ，它做的事情和前面的方法完全一样，但是从**右侧**开始，向后。🔙

## [。找到【索引】()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)

在数组中寻找一个特定的元素可能是一项艰巨的任务，当然，除非它是第一个或最后一个元素。这里，在 ES6 中添加的`.find()`方法真的很有帮助。它只是采用处理标准参数集的**检查函数**，并从给定数组返回**的第一个匹配值**，否则返回`undefined`。

```
const arr = [1,2,3,4];
const matched = arr.find(num => num % 2 === 0); // 2 
```

Enter fullscreen mode Exit fullscreen mode

还有一个 [`.findIndex()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex) 方法，和第一个方法一样，使用匹配函数来查找值，但是返回它的索引而不是原始值。它可以与 [`.indexOf()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf) 或 [`.lastIndexOf()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/lastIndexOf) 相比较，同样可以用来检索与所提供的值相匹配的第一个和最后一个值的索引，但是它没有**像`.findIndex()`那样具有丰富的表达能力。😑** 

```
const arr = [1,2,3,4];
const matched = arr.findIndex(num => num % 2 === 0); // 1 
```

Enter fullscreen mode Exit fullscreen mode

最后一点需要注意的是——如果你只使用`.indexOf()`来检查一个值是否存在于给定的数组中，考虑使用 [`.includes()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes) (ES6 特性)——它返回一个**布尔值**,并且比它的替代品有一点点**更好的性能**。

## [。每()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)

尽管有些人可能期望`.every()`简单地在给定数组的每个元素上运行所提供的函数，但是这里的命名可能会误导人。相反，`.every()`确实在每个元素上运行一个函数，但只是为了让**检查它们是否遵循了提供的指导方针**，并最终返回一个合法的布尔值。检查功能提供了标准的参数集。👌

```
const arr = [1,2,3,4];
const isIncremental = arr.every((num, idx, arr) => {
    const previousIdx = idx - 1;
    if(previousIdx >= 0){
        return num === arr[previousIdx] + 1
    } else {
        return true;
    }
}); // true 
```

Enter fullscreen mode Exit fullscreen mode

## [。copyWithin()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/copyWithin)

对某些人来说，在单个数组的边缘复制数据可能有点复杂且毫无意义。但是，凭借其**优越的性能**🌠(特别是在它的[型数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)的对应物中)，这个方法提供了一个让**快速移动数组元素**的好方法！在这里，您可以传递 1 到 3 个参数:

*   将粘贴复制数据的**目标索引**。由于`.copyWithin()`不改变源数组的长度，元素将被替换，旧数据将被删除。
*   **起始索引**标记要复制的数据的开始(默认为 0 -数组的开始)
*   **结束索引**标记要复制的数据的结束(不包括提供的索引)(默认为`.length` -给定数组的结束)

```
const arr = [1,2,3,4];
arr.copyWithin(0,2); // Array.copyWithin() mutates the source array
arr; // [3,4,3,4] 
```

Enter fullscreen mode Exit fullscreen mode

## [。sort()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)

`.sort()`是这些方法中的一种，顾名思义。在这种情况下，它只是对你的数组进行排序。它可以具有或不具有提供的**比较功能**。默认情况下，所有值都被转换为字符串，并按 [UTF-16](https://en.wikipedia.org/wiki/UTF-16) 代码递增排序**🌐值，即从小到大的数字和按字母顺序排列的字符串。您还可以提供一个函数，该函数接收两个元素作为单独的参数进行比较。这个比较函数应该**返回一个数字**，该数字将用于以给定的方式对提供的值进行排序:**

*   如果函数返回的数字**小于 0** ，则作为**第一个参数**提供的值优先；
*   如果函数返回的数字**等于 0** ，这些值将保持**不变**(规范并没有真正保证)；
*   如果函数返回大于 0 的数字**，则作为**第二参数**提供的值优先；**

```
const arr = [1,2,3,4];
arr.sort((num1, num2) => {
    return num2 - num1;
}); 
// Array.sort() mutates the source array 
```

Enter fullscreen mode Exit fullscreen mode

## [。一些()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)

`.some()`是类似于`.every()`的方法。它检查源数组**中的元素是否满足某些规则**(以**检查函数**的形式提供)，最后返回一个布尔值。不同的是`.some()`只需要**的一个元素** ☝来满足测试，返回一个正值，不像`.every()`需要每个元素。它可以帮助你，例如，检查是否至少有一个值具有给定的属性。提供的测试函数接收一组标准参数(元素、其索引和源数组)

```
const arr = [1,2,3,4];
arr.some((num) => {
    return num % 5 === 0;
}); // false 
```

Enter fullscreen mode Exit fullscreen mode

# 迭代

惊喜惊喜！😲事实上，在 array API 中只有一种方法只执行迭代！只是对那些使用`.map()`、`.reduce()`和类似方法的人大声疾呼，只是为了迭代源数组。这个任务只有一种方法，因此，它应该受到尊重并广为人知。

## [。forEach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)

`.forEach()`，顾名思义- **遍历源数组的每个元素**。提供一个接收标准参数集的函数，它在给定数组的每个元素上运行它。🔁

```
const arr = [1,2,3,4];
arr.forEach((num) => {
    console.log(num); // 1/2/3/4
}) 
```

Enter fullscreen mode Exit fullscreen mode

# 杂项

除了以上所有的类别，数组 API 还提供了更多的方法。下面是其中的一些，它们肯定**不太为人所知**并被使用，但在一些特殊情况下可能会派上用场。

## [。条目()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/entries)

`.entries()`是 3 个**迭代器对象**返回方法中的第一个。数组迭代器对象或所谓的 **iterable** 是一个简单的结构，可以自然地使用`for... of`循环进行迭代，并且只有一个`.next()`方法，在迭代时被称为 under-hood。当被直接调用时，它返回一个包含`value`和`done`属性的**对象**，如果迭代完成，它们分别指示* *当前值* *和**。⏳当再次被调用时，它将从数组中返回**下一个值**，并且该过程将继续，直到源数组结束，此时`done`属性将被设置为**真**。**

由`.entries()`方法返回的迭代器将以**键-值对**(数组)的形式获得其值，其中第一个元素表示**当前索引**和第二个- **各自的值**。您可以将它(以及稍后将讨论的其他方法)与它们的对象对应物进行比较。像`Object.entries()`或`Object.keys()`(不在原型上)这样的函数肯定比它们的数组兄弟更受欢迎，但做的事情相似。

```
const arr = ["a","b","c","d"];
const iterable = arr.entries();
iterable.next(); // {value: [0, "a"], done: false}
iterable.next().value; // [1, "b"]
iterable.next().value; // [2, "c"]
iterable.next(); // {value: [3, "d"], done: true} 
```

Enter fullscreen mode Exit fullscreen mode

迭代完成后**不能重启**。再次做同样事情的唯一方法是用同样的方法创建一个新的**可迭代的**。

但是这种方法的用例是什么呢？嗯，`.next()`方法当然给了你一点点**更多的控制**你的数组是如何迭代的。此外，当希望同时访问元素的值和索引时，由`.entries()`返回的类似键-值的对肯定是有用的。但是，在这些情况下，也许**标准物体**或**地图**(我在 [**上一篇**](https://areknawo.com/rocking-js-data-structures/) 中谈到过)会更好地为你服务。

## [。按键()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/keys)

由于已经涵盖了 iterables 背后的所有复杂性，还有两个类似于`.entries()` - `.keys()`和`.values()`的方法。第一个，顾名思义，返回 iterable，其值等于 source **数组索引**(又名 keys)。🔢它返回代表数组元素索引的数字**而不是键值数组。** 

```
const arr = ["a","b","c","d"];
const iterable = arr.keys();
iterable.next(); // {value: 0, done: false}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

## [。值()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/values)

`.values()`方法再次返回 iterable。这次它的值等于**源数组元素的值**。

```
const arr = ["a","b","c","d"];
const iterable = arr.values();
iterable.next(); // {value: "a", done: false}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

## [。toString()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/toString)

我想说的最后一个方法是`.toString()`。它存在于 JS 对象、函数、字符串、数字、数组等等！可能在每个 JS 对象中(*一切都是对象*)！但是我认为，尽管*无处不在*，但`.toString()`方法仍然没有得到应有的关注。

在其核心，`.toString()`方法只是将数组转换成字符串。返回值采用方括号的形式，数组元素紧密排列在一起，用逗号分隔。

```
const arr = [1,2,3,4];
const str = arr.toString(); // "[1,2,3,4]" 
```

Enter fullscreen mode Exit fullscreen mode

但是它最大的优点就是不用直接调用！

```
const arr = [1,2,3,4];
const str = `${arr}`; // "[1,2,3,4]" 
```

Enter fullscreen mode Exit fullscreen mode

这样，每次你的值需要被转换成一个字符串(比如字符串文字或者串联)，这个**方法就被称为**。这也是你可以用**你自己的实现**自由改变这个方法的事实，这给了你不仅返回自定义字符串的能力，而且在这样做的时候还可以执行某些操作！✨相信我——这可以让你做一些漂亮的**有趣的把戏**！

# 阵列时间！

所以，这些是我个人挑选的一些最有趣和值得一看的数组 API 方法！你认识他们所有人吗？我知道内置的 API 很容易被遗忘，从而导致我们去寻找不需要存在的问题的解决方案。我希望这篇文章至少能帮助你解决一些问题。😀

现在，**你觉得这篇文章**怎么样？在**评论区**写下你的看法，用下面的**一个反应让我知道你是否喜欢！还有，**在 Twitter 上关注我**[**🐦**](https://twitter.com/areknawo)**、[、**我的脸书页面**、](https://www.facebook.com/areknawoblog)并考虑查看我的[个人博客](https://areknawo.com)以保持**最新**⏱这个博客的最新内容。一如既往，**感谢**阅读这篇文章，我们下期再见！✌****