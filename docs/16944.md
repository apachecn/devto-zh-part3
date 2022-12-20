# 新数组(1)= >[空标题 x 1]

> 原文：<https://dev.to/alephnaught2tog/empty-title-x-1-47ng>

想象一下这是艰难的一周。

最后，是时候补充我们每周的巧克力供应了。像往常一样，我们使用 JavaScript 来填充我们的巧克力供应。

在伪代码中，“一盒 21 个相同种类的巧克力”类似于:

> 1.  Get a box.
> 2.  Make sure there is enough room in the box for 21 chocolates.
> 3.  As long as there are empty slots:
>     *   Get a chocolate from a giant bag of chocolate. Put a piece of chocolate in the slot you see.
>     *   Move to the next slot.

很合理，对吧？让我们试一试！

(注意:所有代码片段都应该可以在 repl 或控制台中运行，如果您愿意，只需通过复制粘贴即可。)

## 尝试 1: `.map`

对于第一次挥杆，也许我们可以尝试`map` :

```
let chocolate = {
    kind: 'dark',
    filling: 'raspberry ganache'
};

// Prep our box and make sure it has 21 slots
let weeklyChocolateSupplyBox = new Array(21);

// Put one chocolate into every slot
weeklyChocolateSupplyBox.map(_emptySlot => chocolate); 
```

Enter fullscreen mode Exit fullscreen mode

(如果您对下划线(即`_emptySlot`)感到疑惑，这意味着该变量要么不重要，要么未被使用。一些语言把它作为一条规则来执行，比如灵药；在这里，纯粹是约定俗成。)

到目前为止，一切顺利:我们制作了一个有 21 个槽的数组，用`map`循环遍历它，并在每个槽中放入一块巧克力。

我们实际上在每个槽中放了完全相同的巧克力，这在现实世界中并不理想——对任何一种巧克力的任何改变都会影响每一种巧克力，因为它们都是同样的巧克力。

> `(╯°□°）╯︵ [Ɛ x ʎʇdɯǝ]`

也许不太意外，它不起作用。如果您在控制台中运行该代码片段，您将得到类似于:`[empty × 21]`的结果，而不是包含 21 块相同巧克力的数组。

至少可以说，不太理想。

## 尝试二:`for (let index ... )`

虽然我喜欢尽可能地使用各种数组方法，例如`forEach`、`filter`、`map`等。我发现，自从我第一次学习 C 风格的`for`循环，当事情不顺利的时候，我经常回头引用它们。类似地，作为健全性检查，我经常在循环前后注销一些东西，这样我就可以确保没有真正奇怪的事情发生，比如在错误的文件中，等等。

说到底，循环就是循环，用你和别人最清楚的东西。

所以，我们再试一次！

```
// same as before
chocolate = {
    kind: 'dark',
    filling: 'raspberry ganache'
};

// assign the variable a whole new array to reset.
weeklyChocolateSupplyBox = new Array(21);

console.log('before loop');
for (let index = 0; index < weeklyChocolateSupplyBox.length; index += 1) {
    console.log('loop number %d', index);
    weeklyChocolateSupplyBox[index] = chocolate;
}

console.log(weeklyChocolateSupplyBox);
console.log('after loop'); 
```

Enter fullscreen mode Exit fullscreen mode

这次，我们成功了。我们有一个盒子，里面有 21 块巧克力，如你所愿！太棒了。

## 尝试三:`for ... of`

假设我没有使用老式的`for`循环:假设我使用了一个`for ... of`循环——毕竟，我想循环这个数组并把东西放进去，对吗？这样，我也不需要自己增加索引，也不用担心忘记了条件或什么。太好了！

所以让我们来写代码，用一个`for ... of`循环来代替。我们像以前一样开始，并勾画出我们的`for`循环的框架。

```
chocolate = {
    kind: 'dark',
    filling: 'raspberry ganache'
};

// assign the variable a whole new array to reset.
weeklyChocolateSupplyBox = new Array(21);

console.log('before loop');
for (let emptySlot of weeklyChocolateSupplyBox) {
    console.log('emptySlot', emptySlot);
    // Put a chocolate into our emptySlot
}
console.log('after loop'); 
```

Enter fullscreen mode Exit fullscreen mode

...但是循环内部是什么呢？我们有`emptySlot`——但是*没有*的方法在里面加入巧克力。如果我们现在运行这个，我们只会看到`emptySlot undefined`注销了 21 次。没什么帮助。

## 尝试四:`for ... in`

在 JavaScript 中，一切都是对象。数组也是——特别是，数组是由`Array`构造函数创建的对象。根据定义，它们有一个`length`属性和数字有序键。

还有另一种我们没有尝试过的`for`循环:`for ... in`，它遍历一个对象的属性。对于像对象文字这样的东西，它在属性名上循环；对于数组，它遍历索引。有点奇怪，但是如果你仔细想想，这似乎有点合理——我们可以使用字符串键和数组索引来设置值，然后通过键访问该值，对吗？

```
const dog = { name: 'Simon', age: 13, weight: 50 };
const someNumbers = [3, 1, 4];
for (let key in dog) {
    console.log('dog key', key); // 'name', then 'age', then 'weight'
    console.log('dog value', dog[key]); // 'Simon', then 13, then 50
}

for (let key in someNumbers) {
    console.log('someNumbers key', key); // '0', then '1', then '2'
    console.log('someNumbers value', someNumbers[key]); // 3, then 1, then 4
} 
```

Enter fullscreen mode Exit fullscreen mode

好的，酷，这里没有什么太有趣的，除了也许可以用数组来做。

所以，我们再来一次巧克力实验。普通的`for`循环起作用了——让我们用一个`for ... in`循环做同样的事情，我们可以像以前一样使用索引将它添加到数组中。

```
chocolate = {
    kind: 'dark',
    filling: 'raspberry ganache'
};

// assign the variable a whole new array to reset.
weeklyChocolateSupplyBox = new Array(21);

console.log('before loop');
for (let emptySlotIndex in weeklyChocolateSupplyBox) {
    console.log('emptySlotIndex', emptySlotIndex);
    weeklyChocolateSupplyBox[emptySlotIndex] = chocolate;
}
console.log('after loop'); 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们看到了`before loop`和`after loop`，以及...实际上没别的了。

## 有什么区别？

所以，我们尝试了很多方法:

*   失败-什么也没做
*   `for ... of`循环:失败-无法添加巧克力
*   循环:失败——从来没有循环过！
*   基本`for`循环:有效！

然而，这些都没有回答这个问题:为什么一个`for`循环有效而其他选项失败，而`for ... in`从不循环？

答案在于 JavaScript 本身的规范。

数组构造器*做*创建一个`Array`对象，并将其`length`设置为给定<sup>T5】1T7 的(单个，数字)值。</sup>

然而，它所做的*而不是*是在数组对象上设置索引(这只是键，记住，恰好是数字)。

```
// This is about what happens:
const newArray = {
    length: 2
};

// NOT this:
const badNewArray = {
    length: 2,
    '0': undefined,
    '1': undefined
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果你曾经试图从一个对象中移除某些东西——真正地移除它，不仅仅是给它一个`undefined`值，而是完全移除属性*——你知道`chocolate['filling'] = undefined`不会削减它。财产将仍然在那里，只是用`undefined`作为它的价值。*

 *要删除一个属性，您必须`delete`它:`delete chocolate['filling'];`。之后，如果你检查对象，将有*没有*键称为`filling`存在。如果我们查看它的键，我们不会看到`filling`被列出。

那么，如果你从一个数组中取出一个索引会发生什么呢？

```
const someOtherArray = ['value at 0', 'value at 1', 'value at 2'];

console.log(someOtherArray); // ["value at 0", "value at 1", "value at 2"]
console.log(someOtherArray.length); // => 3

delete someOtherArray[1];

console.log(someOtherArray.length); // => still 3
console.log(someOtherArray);
// Chrome:  ["value at 0", empty, "value at 2"]
// Firefox: ["value at 0", <1 empty slot>, "value at 2"]
// Safari:  ["value at 0", 2: "value at 2"] 
```

Enter fullscreen mode Exit fullscreen mode

每个浏览器都显示相同的东西，只是不同:一个长度为 3 的数组，里面只有两个东西，分别是 0 和 2。索引 1 处不再有任何东西——因为没有索引 1。每个数组的长度仍然是 3。

这解释了为什么`for ... in`失败得如此严重:`for ... in`循环对一个对象的键起作用:没有键(索引)可供它枚举。类似地，如果我们在删除索引之前和之后都执行了上面的循环，我们将在删除索引之前执行 3 次循环，在删除索引之后执行 2 次循环。

## 一个不太知名的符号

这里有另一条线索:`[...new Array(3)]`做了我们可能最初预期的事情，并给了我们`[undefined, undefined, undefined]`。

答案是**迭代器**；具体来说，就是对象上的`Symbol.iterator`的值。(`Symbol` s 是一个 JavaScript 原语，它的值是唯一的，并且经常被用作标识符——很像其他语言中的原子。)

如果一个对象有一个`Symbol.iterator`，那么这个对象就是可迭代的:它有一个迭代器，一个遵循*迭代器*协议的对象。迭代器非常简洁，*非常*强大——它们是`async`、`await`、生成器、承诺、扩展操作符、`for ... of`等的核心；它们允许异步地进入和退出不同的执行上下文。

然而，对于我们的目的来说，知道迭代器本质上跟踪你在循环中的位置就足够了。许多 JavaScript 对象都有一个默认的迭代器——数组，以及任何可以传播的东西(如上使用`...`)。

特别是，默认的迭代器规范 <sup>[2](#foot_2)</sup> 是这样说的:

> *   From `index = 0`.
> *   As long as `index` is less than `array.length`:
>     *   `Yield` `array[index]`
>     *   Incremental index of value, `index += 1`
>     *   Keep moving

<small>许多其他数组方法使用类似的逻辑——例如，`toString`使用`join`，它有类似的算法。</small>

当你访问一个不在对象上的属性时，你会得到什么？在某些语言中，它根本无法编译；然而，在 JavaScript 中，你不会得到一个错误，你只会得到`undefined`——当然，如果键*是*的话，它也可以是这个值。

```
const withKeyAndUndefined = { apples: undefined, pears: 3 };
const withKeyAndValue = { apples: 12, pears: 99 };
const withoutKey = { pears: 74 };

console.log(withKeyAndUndefined['apples']); // => undefined
console.log(withKeyAndValue['apples']);     // => 12;
console.log(withoutKey['apples']);          // => undefined 
```

Enter fullscreen mode Exit fullscreen mode

至于`map`失败也一样？

良好的...对于`map`(以及`forEach`和其他类似的方法)的规范 <sup>[3](#foot_3)</sup> 阐明了给定的回调仅*对于那些“没有丢失”的值执行，即非空的槽或定义了索引的地方(因此，在构造之后没有任何地方)。* 

```
const yetAnotherArray = new Array(5);

yetAnotherArray.map(
    value => {
        throw new Error('never gonna happen');
    }
).fill(
    null // now we put something in every spot
).map(value => {
    console.log('now, this will show "null": ', value);
    return value;
}); 
```

Enter fullscreen mode Exit fullscreen mode

与此同时，我们的基本`for`-循环马上开始工作:因为*我们*通过在那个键下设置一个值来创建那些索引，同样的方式我也可以在`const dog = {name: 'Simon'}; dog.favoriteFood = 'peanut butter';`上做，而`favoriteFood`从未被定义在原始对象上。

```
const array = new Array(5);

for (let index = 0; index < array.length; index += 1) {
    // does 'index' exist? Yes! It's its own variable, after all
    console.log('index', index);
    console.log(`before: ${index} in array?`, index in array);
    array[index] = 'whee';
    console.log(`after: ${index} in array?`, index in array);
} 
```

Enter fullscreen mode Exit fullscreen mode

方便的是，有一个方法可以`fill`一个任意值的数组。我们也可以用在这里。

对于一个简单的例子，我们可以只做`new Array(5).fill(chocolate)`；然而，对于任何更复杂的东西，我们需要首先用一些东西`fill`数组——任何东西，甚至是`null`或`undefined`。

```
weeklyChocolateSupplyBox = new Array(21).fill(chocolate);
console.log(weeklyChocolateSupplyBox);

const rangeFrom_1_to_10 = new Array(10).fill(null).map((_null,index) => index + 1);

console.log(rangeFrom_1_to_10); 
```

Enter fullscreen mode Exit fullscreen mode

不过，请记住，我们实际上在这里最终得到的是 21 种相同的巧克力——如果我们融化一种巧克力，它们都会融化，因为我们真正做的是通过一些真正壮观的量子糖果将相同的巧克力放入每个插槽中。(然而，巧克力似乎比一串字符串或数字更令人愉快。)

* * *

1.  [数组构造函数规范](https://www.ecma-international.org/ecma-262/6.0/#sec-array-len)
2.  [迭代器规范](https://www.ecma-international.org/ecma-262/6.0/#sec-%arrayiteratorprototype%.next)
3.  [`map`规格](https://www.ecma-international.org/ecma-262/6.0/#sec-array.prototype.map)*