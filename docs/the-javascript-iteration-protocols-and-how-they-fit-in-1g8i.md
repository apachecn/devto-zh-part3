# JavaScript 迭代协议以及它们如何适应

> 原文：<https://dev.to/kenbellows/the-javascript-iteration-protocols-and-how-they-fit-in-1g8i>

ECMAScript 2015 (ES2015，又名 ES6)推出的最酷、也是最被低估的功能之一是一对[迭代协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)，它定义了 JavaScript 中的“迭代器”和“可迭代对象”。这些协议为我们提供了一种本地方式来创建自定义的容器、列表和类似伪列表的东西，当与 ES2015 中引入的其他两个功能结合时，即`for...of`循环和生成器函数(`function*`，它们给了我们一些非常好的新功能。

# 案例分析:链表

作为一个具体的例子，让我们看看我们如何实现和循环一个[链表](https://en.wikipedia.org/wiki/Linked_list)的三种不同方式:

*   老派的非迭代器方式
*   使用迭代协议
*   使用发电机

如果你需要快速复习什么是链表，并且感觉有点困惑；关于我在维基百科上链接的文章，这里有一些基础知识:一个链表可以被认为是一个用一堆独立连接的节点构建的东西的列表，每个节点只知道它自己的值和列表中的下一个东西，有一个知道列表的开始(“头”)和结束(“尾”)的父对象。通过创建一个新节点，将当前尾部链接到该节点，并更新父节点的尾部引用，可以添加到列表中。有很多变体，比如双向链表，对于某些应用，它们比传统数组有很多性能优势，但我不会在这里深入讨论，因为它很快就变得复杂了；如果你对这些不熟悉，可以看看维基百科的文章，在谷歌上找找文章，也许还有关于“数据结构”的 MOOC 课程。

# 联系列举了守旧派的方式

下面是一个使用 ES6 类的链表的简单实现，但没有使用迭代器:

```
class LinkedList {
    constructor() {
        this.head = this.tail = null
    }
    push(val) {
        const next = {val, next: null}
        if (this.head === null) {
            this.head = this.tail = next
        }
        else {
            this.tail.next = next
            this.tail = next
        }
    }
    forEach(fn) {
        let curr = this.head
        while (curr !== null) {
            fn(curr.val)
            curr = curr.next
        }
    }
}

// example
const l = new LinkedList
l.push(10)
l.push(20)
l.push(30)
l.forEach(n => console.log(n)) 
```

好吧，我们来分析一下。

当`LinkedList`在`constructor()`中第一次初始化时，它里面什么都没有，所以它的`head`和`tail`属性都被设置为`null`。

方法向列表中添加一个新元素。每次调用`push()`时，都会创建一个新对象来保存新添加的值，该对象有两个属性:

*   保存传入值的属性
*   一个指向列表中下一个节点的`next`属性

注意，每个节点的`next`属性最初被设置为`null`，因为到目前为止，一个节点总是作为列表中的最后一项创建的。

我们分两步将这个新节点声明为列表的新尾节点:

*   将列表当前`tail`的`next`属性设置为新节点
*   将列表的`tail`属性设置为新节点

这里还有一个额外的步骤来检查`head`是否为空，以处理对`push()`的第一次调用，我确信这个类可以被重构以避免重复检查，但这只是一个玩具示例，所以现在忽略低效率。

现在重要的部分是:`forEach()`方法。这是我们迭代链表内容的地方。我们不能使用传统的`for (let i=0; i<list.length; i++)`循环来迭代节点，因为除了`head`和当前的`tail`之外，我们不能直接(也称为“随机”)访问任何节点。相反，我们需要从`head`节点开始，一次向下遍历列表中的一个节点，在每一步中使用当前节点的`next`属性来查找下一个节点，直到我们到达一个`null`。现在，我选择将它写成一个`while`循环，因为我认为它更容易阅读，但是它实际上可以写成一个`for`循环:

```
forEach(fn) {
    for (let curr=this.head; curr !== null; curr=curr.next) {
        fn(curr.val)
    }
} 
```

你挑吧，它们是等价的。

现在，这段代码还不错，但是任何想要使用你的类的代码都必须使用`forEach`方法，而不是更好的结构，比如`for...of`循环。这可能会降低它与其他数据类型(如数组)的兼容性。如果您正在编写一些基于数组的复杂处理代码，但是过了一段时间后意识到您实际上应该使用一个链表，那么发现您需要返回并重写一堆使用`for...of`循环的代码以便切换可能会令人沮丧，并且您可能会决定您没有时间。这可能看起来像一个愚蠢的例子，当然这是一个故意简单化的玩具案例，但是作为一个通用规则，交叉兼容是一件好事。

所以让我们重构一下，看看我们如何利用迭代协议来使我们的类`for...of`循环兼容。

# 迭代协议

不过，首先，让我们先了解一下这些协议是什么。其中有两个:**迭代协议**和**可迭代协议**。两者都很简单，所以我们很幸运。

## 迭代器

迭代器协议更有趣。为了让一个对象有资格成为“迭代器”，它只需要一样东西:一个`next()`方法。每次调用`next()`时，它都必须返回一个具有两个属性的对象:`value`，表示要迭代的下一个值，以及`done`，表示是否还有下一次迭代。

具体来说，在每次调用时，如果至少还有一个值需要迭代，函数应该返回一个对象，如下所示:

```
{ value: 'next value here', done: false } 
```

如果没有什么可生成的，函数应该返回这样一个对象:

```
{ value: undefined, done: true } 
```

一会儿我将向您展示一些示例代码。但是首先我们需要谈谈...

## 可重复项

iterable 协议甚至比 iterator 协议更简单。从概念上讲，iterable 是任何可以在需要时产生迭代器的对象。从技术上来说，如果一个对象有一个带有特殊名称的方法(等等),这个方法在被调用时会返回一个迭代器，就像上面定义的那样，那么这个对象可以算作一个 iterable。

现在，关于那个特别的名字。ES2015 另一个被低估的特性是引入了一种新的原始类型`symbol`。这里有很多要讨论的，但长话短说，符号可以用作全局唯一的对象键，以确保每个人都在谈论同一件事，而不是两个同名的不同想法。(关于符号还有很多要讨论的，我*强烈推荐*阅读 Mozilla Hacks 博客的文章， [ES6 深度:符号](https://hacks.mozilla.org/2015/06/es6-in-depth-symbols/)，实际上还有 ES6 深度系列的其余部分。)

对我们来说，重点是有一些内置的、规范定义的符号用于实现协议，比如 iterable 协议，它使用全局键`Symbol.iterator`来标识返回迭代器的方法。下面是一个简单的类，它创建了一个 iterable 来循环传递给构造函数的参数:

```
class ArgsIterable {
    constructor(...args) {
        this.list = args
    }
    [Symbol.iterator]() {
        const list = this.list
        let i=-1
        return {
            next() {
                i += 1
                if (i<list.length) {
                    return { value: list[i], done: false }
                }
                else {
                    return { done: true }
                }
            }
        }
    }
} 
```

那么这是如何工作的呢？让我们一步一步来看:

```
const iterable = new ArgsIterable(1,3,5,7)
const iterator = iterable[Symbol.iterator]()
console.log(iterator.next())
console.log(iterator.next())
console.log(iterator.next())
console.log(iterator.next())
console.log(iterator.next())
console.log(iterator.next())
/* output:
{value: 1, done: false}
{value: 3, done: false}
{value: 5, done: false}
{value: 7, done: false}
{done: true}
{done: true}
*/ 
```

前 4 次`iterator.next()`被调用，我们在数组中得到一个值，我们被告知还没有到达末尾。然后一旦我们到达终点，我们开始总是发送`{done: true}`。

这种方法的主要优点是`for...of`循环理解这个协议:

```
for (const n of new ArgsIterable(1,3,5,7)) {
    console.log(n)
}
/* output:
1
3
5
7
*/ 
```

如果这看起来工作量很大，你没有错，但是有一个解决方案:发电机。但是我们一会儿就会讲到。现在，让我们回到我们的`LinkedList`课堂。

# 可迭代链表

现在我们已经了解了迭代器和可迭代对象的工作原理，让我们把类变成一个可迭代对象。

```
class LinkedList {
    constructor() {
        this.head = this.tail = null
    }
    push(val) {
        const next = {val, next: null}
        if (this.head === null) {
            this.head = this.tail = next
        }
        else {
            this.tail.next = next
            this.tail = next
        }
    }
    [Symbol.iterator]() {
        let curr = this.head
        return {
            next() {
                if (curr === null) {
                    return { done: true }
                }
                else {
                    const next = { value: curr.val, done: false }
                    curr = curr.next
                    return next
                }
            }
        }
    }
}

// example
const l = new LinkedList
l.push(10)
l.push(20)
l.push(30)
for (const n of l) console.log(n)
/* output:
10
20
30
*/ 
```

不太可怕，对吧？`[Symbol.iterator]()`用一个`next()`方法返回一个对象，用一个局部变量`curr`跟踪当前节点，就像我们在前面的`forEach()`方法中一样。每次调用`next()`时，我们检查`curr`是否为`null`。如果是这样，我们让打电话的人知道我们结束了；如果没有，我们准备我们的响应对象，将列表中的`curr`向下移动一个节点，为下一次迭代做准备，然后返回我们的响应对象。有点像`forEach()`的控制较少的版本，用户可以在准备好的时候抓取列表中的下一个项目。如果您运行最后的示例代码，您会看到我们的`LinkedList`类*的实例现在只使用`for...of`循环来运行*！多酷啊。

# 免费阵列传播！

如果你不相信，让我向你展示一个非常好的好处，当你实现可迭代协议时，它是免费的:用 ES2015 spread 操作符扩展到一个数组！如果您需要在主处理中使用一个链表，但是想要一个包含结果的数组，也许是为了运行一些数组方法，那么您很幸运！只需将您的`LinkedList`实例分散到一个数组中:

```
const list = new LinkedList
list.push(10)
list.push(20)
list.push(30)
list.push(50)
// magic!
const final = [...list].map(n => n*2).filter(n => n%3 === 0)[0]
console.log(final)
// output: 60 
```

这是因为 spread 操作符，就像`for...of`循环一样，依赖于幕后的 iterable 协议来生成结果数组的内容。

正如我上面提到的，这可能仍然感觉像是大量的脑力劳动和代码，而没有那么大的好处。但是正如我也提到的，有一个解决方案:

# 发电机

另一个我最喜欢的被低估的 ES2015 功能，生成器在教程中经常被称为“可减少的功能”。这是思考它们的一种非常直观的方式，但是我会稍微调整一下:我更愿意称它们为可减少的可迭代对象。我们来看一个简单的例子，然后我来解释:

```
function* countTo(n) {
    for (let i=1; i<=n; i++)
        yield i
}

// example
for (const n of countTo(5))
    console.log(n)
/* output:
1
2
3
4
5
*/ 
```

你可能已经猜到了，这里的关键是`yield`关键字。第一次通过`for...of`循环时，生成器函数从顶部开始运行，直到遇到那个`yield i`，此时它返回`i`的值(sorta 请原谅)，并在那里“暂停”该函数，因此有了“pauseable”描述符。下一次循环时，它从停止的地方继续前进，直到碰到另一个`yield`，然后再次暂停。这一直持续到函数没有到达`yield`，而是到达`return`语句，或者在我们的例子中，到达函数的结尾。但是它到底是如何与`for...of`循环进行通信的呢？这个循环不是需要一个 iterable 吗？

如果你直接调用`countTo(5)`并查看结果，你会看到一些非常有趣的东西。以下是我在浏览 Chrome 的开发工具时得到的一些信息:

<figure>

[![](img/13b8ff57d333701dce49f1c5bb92f344.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UO30V5Li--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jazb258bfeaol5x6kogp.PNG)

<figcaption>Poking at a generator in Chrome's dev tools</figcaption>

</figure>

```
> x = countTo(5)
  countTo {<suspended>}
> x.next
  f next() { [native code] }
> x[Symbol.iterator]
  f [Symbol.iterator]() { [native code] } 
```

这里重要的是，调用生成器并不直接返回值:它返回一个引擎描述为“暂停”的对象，这意味着生成器函数的代码还没有运行。有趣的是，该对象既有一个`next()`方法*，又有一个*方法`[Symbol.iterator]`。换句话说，它返回一个既是可迭代的*和*又是迭代器的对象！

这意味着生成器既可以作为独立的序列生成器使用，就像上面的`countTo(n)`方法，也可以作为一种*非常简单的*方法来使你的对象可迭代！

# 带生成器的链表！

让我们再次回到我们的`LinkedList`类，用生成器
替换我们的自定义`[Symbol.iterator]`方法

```
class LinkedList {
    constructor() {
        this.head = this.tail = null
    }
    push(val) {
        const next = {val, next: null}
        if (this.head === null) {
            this.head = this.tail = next
        }
        else {
            this.tail.next = next
            this.tail = next
        }
    }
    *[Symbol.iterator]() {
        let curr = this.head
        while (curr !== null) {
            yield curr.val
            curr = curr.next
        }
    }
}

// example
const l = new LinkedList
l.push(10)
l.push(20)
l.push(30)
for (const n of l) console.log(n)
/* output:
10
20
30
*/ 
```

关于`[Symbol.iterator]`方法的两件事。首先，注意我们必须在它的前面加上一个星号来表示它是一个生成器函数。其次，也是最重要的，看看方法的主体:看起来熟悉吗？它几乎与前面的`forEach()`方法完全相同，只是用`yield`关键字替换了一个回调函数！

因为生成器返回一个实现迭代器协议的对象，所以生成器使得*很容易*使你的对象可迭代！您可以使用各种有趣的存储模式和遍历算法，这没关系:生成器让它变得简单！

# 再举一个例子:ImageData

举个更具体的例子，我想花点时间谈谈画布。我个人喜欢使用 HTML5 Canvas 元素来处理图像。您可以使用原生的`Image`对象加载图像，然后将它绘制到画布上，抓取它的`ImageData`对象，并直接操作像素值。但是`ImageData`有一个问题:它是由计算机存储的原始像素数据，这意味着它不是像`[{r:255,b:128,g:0,a:255},...]`那样存储为像素数组，而是一个长而扁平的字节数组，像`[255, 128, 0, 255, ...]`。这意味着要在像素上循环，你通常需要这样做:

```
for (let i=0; i<imgData.length/4; i++) {
    const p = i*4
    const pixel = {
        r: imgData[p],
        g: imgData[p+1],
        b: imgData[p+2],
        a: imgData[p+3]
    }
    processPixel(pixel)
} 
```

这是...*好的*，但是如果你需要重复写一堆就很烦了，而且作为一个需要回调的 util 函数也很奇怪:

```
function processPixels(imgData, processPixel)
    for (let i=0; i<imgData.length/4; i++) {
        const p = i*4
        const pixel = {
            r: imgData[p],
            g: imgData[p+1],
            b: imgData[p+2],
            a: imgData[p+3]
        }
        processPixel(pixel)
    }
} 
```

复试...总的😢

另一种选择是循环遍历`ImageData`缓冲区，首先将其转换为一个数组，然后使用`for...of`循环遍历数组，使其更具可读性，但是考虑到现在图像有多大，这是一个*巨大的*内存浪费。

那么，如果我们写一个小的生成器函数，让我们更容易地循环数组，而不浪费大量的内存，会怎么样呢？这是生成器的一个很大的好处:它们感觉你只是在迭代一个数组，但实际上，一次只有一个元素存在于内存中！

```
function* getPixels(imgData) {
    for (let i=0; i<imgData.length/4; i++) {
        const p = i*4
        const pixel = {
            r: imgData[p],
            g: imgData[p+1],
            b: imgData[p+2],
            a: imgData[p+3]
        }
        yield pixel
    }
}

for (const pixel of getPixels(imgData)) {
    // process pixel
} 
```

干净简单！

# 结论

ES2015 规范给我留下最深刻印象的事情，甚至比漂亮的新功能本身更深刻的是，在制作功能时花了多少心思，这些功能以非常好的方式将*与*结合在一起，使 JavaScript 成为一种非常有凝聚力的语言。类语法、迭代协议、`for...of`循环、生成器、符号和数组扩展运算符都是 ES2015 中添加的功能，它们都可以如此流畅地结合在一起。这真的是一个令人印象深刻的壮举，随着 ES2016-2018 的推出，它只会变得更好。TC39 提案流程和从中涌现的特性给我留下了深刻的印象。我希望它保持这样！正是这些特性让我对 JavaScript 和网络的未来充满期待。

# 进一步阅读/观看

*   [“深入了解 ES6”——Mozilla Hacks](https://hacks.mozilla.org/category/es6-in-depth/)——非常非常好的博客系列，详细介绍了 ES6 的主要特性
*   [“JavaScript 中的迭代器和生成器”——funfunfunfunction，YouTube](https://www.youtube.com/playlist?list=PL0zVEGEvSaeG2T5n8FuPGb11JHea7idb9)——很棒的播放列表讨论了我在这里提到的主题，并进一步深入到异步领域，每个视频都以非常清晰的方式建立在之前的视频之上
*   [`for await...of` - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for-await...of) - `async`用于通过异步流程进行迭代的循环
*   [“异步迭代器和生成器”——杰克·阿奇博尔德](https://jakearchibald.com/2017/async-iterators-and-generators/)