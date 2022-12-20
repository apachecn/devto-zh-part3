# 理解 JavaScript 中的 Spread 运算符

> 原文：<https://dev.to/marinamosti/understanding-the-spread-operator-in-javascript-485j>

`{...object}`

我第一次看到它的时候，我真的不知道它应该是什么意思，甚至是做什么，但是在 JavaScript 中利用 spread 操作符的能力绝对是值得的。

在我最新的[新手动手 Vue](https://dev.to/vuevixens/hands-on-vuejs-for-beginners-part-5--1jbi)系列中，我在教程中使用了一个 spread 操作符，并承诺我会发布一个帖子来解释它是什么，以及它是如何工作的，为任何还不熟悉它的人。

免责声明。spread 运算符是 ES6 的一个特性，因此并不是每个浏览器都支持它(IE 又罢工了)。然而，你可以放心，它将在所有主流和现代浏览器上工作。[浏览器兼容性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#Browser_compatibility)

# 潜入

**MDN** 是这样解释的:

> Spread 语法允许在需要零个或多个参数(对于函数调用)或元素(对于数组文本)的地方扩展可迭代对象，例如数组表达式或字符串，或者在需要零个或多个键值对(对于对象文本)的地方扩展对象表达式。

我不知道你怎么想，但对我来说，当我试图学习这一切的时候，它是超级令人畏惧和困惑的。所以让我们把它分解成容易消化的部分。

假设你有一个函数，你想把三个值相加:

```
function add(a, b ,c) {
  return a + b + c;
} 
```

现在想象一下，你有一个数组值:

```
const values = [1, 2, 3]; 
```

现在，如果我们问自己，我们如何把这些值放入加法函数中。一个实用的方法可能是这样做:

```
const sum = add(values[0], values[1], values[3]); 
```

我的意思是，它有点工作。对吗？但是稍微动一下，当你有一个函数可以接受动态数量的参数时会发生什么？或者更简单，如果我们有这样一个场景，这个函数需要 20 个值，我给你一个包含 20 个数字的数组。

这就是事情开始变得非常荒谬的时候，你打算通过索引直接访问所有的值吗？(我知道有很多方法可以用像`reduce`这样的数组函数做到这一点，但是为了举例，我就不多说了。)

考虑下面的例子:

```
function sum(a,b,c,d,e,f,g,h,i,j,k,omgwhy) {
 return a+b+c+d+e+f+g+h+i+j+k+omgwhy;
}

const values = [1,2,3,4,5,6,7,8,9,11,12]; 
```

这就是 spread 操作符的闪光点！想象一下这个`values`数组是一个盒子，它以一种非常整洁有序的方式包含了所有这些数字，每个数字都有一个索引——并且完美地包含了它。

当我们像这样访问一个数组的索引值时,`values[1]`我们非常干净利落地取出那个单个值，这就是我们在第一个例子中所做的。但是继续这个比喻，如果我们想按照盒子里的顺序取出所有的值呢？

也许能够做这样的事情很酷，但是我们却像这样输入。

在这种情况下，你会说:

```
const result = sum(...values); 
```

Tada！现在，仔细观察，因为当从数组中取出值时，它们不仅仅被抛到风中，它们都进入到函数中各自的参数中。`a`会得到`1`，`b`会得到`2`以此类推。

# 合并

好的，所以**将**数组扩展到函数中是很好的。但老实说，我没怎么用过它。然而，合并两个数组或两个对象变得轻而易举！

让我们编造一个例子:

```
const favoriteGames = ['Zelda', 'Metroid'];
const favoriteBands = ['Queen', 'RHCP'];

const lifeFavorites = [...favoriteGames, ...favoriteBands]; 
```

所以首先我们只是定义了几个数组作为例子，`favoriteGames`和`favoriteBands`。但是让我们仔细看看第三行。

我们将在这里存储合并的结果，不要太花哨，我们将它设置为一个新的数组。

通知`= [somestuffshere];`

理解这一点非常重要，当你扩散一个数组或者一个对象，这些扩散值需要以某种方式被使用，你需要自己清理。所以在这种情况下我们说:

好了，JS，抓取这两个数组盒子的内容，把它们里面所有的垃圾都转储到这个新的盒子里，并把它叫做`lifeFavorites`。值得庆幸的是，真正的垃圾倾倒是以一种非常有序的方式进行的，价值观的顺序将得到尊重。

所以结果实际上会是:

`['Zelda', 'Metroid', 'Queen', 'RHCP']`

[https://codepen.io/immarina/embed/xMvWyO?height=600&default-tab=result&embed-version=2](https://codepen.io/immarina/embed/xMvWyO?height=600&default-tab=result&embed-version=2)

# 制作副本

使用 spread 操作符的另一个好方法是复制一个数组或对象。在 JavaScript 中，当你将一个对象或数组赋给一个变量时，你实际上是在存储一个叫做“指针”的东西，它的工作方式有点像对象存储的地址。

所以如果你做了:

```
const obj = {name: 'Marina'};
const anotherObj = obj; 
```

这里的大问题是`obj`和`anotherObj`实际上都指向同一个对象。所以如果你改变其中一个，它们都会改变，因为讨厌的**指针**指向同一个对象。

在像 **Vue** 这样的框架中，这变成了一个真正的问题，调试起来也很痛苦，因为在某些情况下，比如制作**道具**的副本时，你永远不会想要修改原始对象。在**反应**中，这也在改变状态时使用，你不应该直接修改值。

那么，我们如何使用 spread 操作符进行复制呢？

```
const baseObject = {name: 'Marina'};
const myCopy = {...baseObject};

const baseArray = [1, 2, 3];
const arrayCopy = [...baseArray]; 
```

需要注意的重要一点是，当使用 spread 操作符复制任何对象/数组时，复制将是浅层的。这意味着如果你的对象有一个子对象，那么`child`仍然有一个指向原始对象的指针。(感谢在评论中指出这一点的所有人)

```
const original = { name: 'Parent', child: { name: 'Child' } };
const copy = {...original};

// The thing to note here is that the COPY will have a child property, but this
// child property is actually pointing to the same exact object as ORIGINAL

// So:
// original.child === copy.child 
```

就是这样！我们再一次将我们的盒子倒入另一个包含它的盒子中，这将确保最终的对象/数组实际上是原始对象/数组的副本——并且您可以安全地修改它们，而不用担心弄乱第一个。