# Javascript Array(): WAT？

> 原文：<https://dev.to/jacoby/javascript-array-wat-1ie2>

这从我尝试在 Javascript 中获得类似于`range()`函数的东西开始。Perl，我选择的语言，允许我用`my @hedgehog = 0..4`创建一个数组，给我`0,1,2,3,4`作为一个数组。

*长*方式是用一个`for`回路:

```
function range(min, max) {
  let x = [];
  for (let i = min; i <= max; i++) {
    x.push(i);
  }
  return x;
} 
```

如果你喜欢递归，那么 Dev.to 上的 Jason Yu 有没有介绍过 :

```
function range(start, end) {
  if(start === end) return [start];
  return [start, ...range(start + 1, end)];
} 
```

知道了这一点，我开始想，肯定有一种方法可以用[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)来做这样的事情。

如果我们已经有了一个数组，我们可以很容易地将它重置为从零开始的范围:

```
let hedgehog = [12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29];
let shrews = hedgehogs.map((n,i)=>i);
// [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17] 
```

所以，我开始考虑我们可以创建特定大小的数组，比如`Array(5)`，那么如果我们创建一个空数组，然后将`map`放入一个范围，会怎么样呢？

```
hedgehogs = Array(5);
shrews = hedgehogs.map((n,i)=>i);
console.log(JSON.stringify(shrews));
console.log(JSON.stringify(hedgehogs));
// [null,null,null,null,null]
// [null,null,null,null,null] 
```

所以，*那是*一个东西。

但是是什么东西*呢？*

[`map`为数组中的每个元素依次调用一次提供的`callback`函数**，并根据结果构造一个新数组。`callback`只对数组中已经赋值的索引调用，包括*未定义的*。对于数组中缺失的元素(即从未设置、已删除或从未赋值的索引)，调用的是**而不是**。**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)(强调 Mozilla)

所以，`Array(5)`是一个空值数组，不仅仅是空值，而是**从未被设置过的**空值，这些空值`map`不会触及。怎么办，怎么办…

装满它们。

`fill`他们。

```
hedgehogs = Array(5);
shrews = hedgehogs.fill().map((n,i)=>i);
console.log(JSON.stringify(shrews));
console.log(JSON.stringify(hedgehogs));
// [0,1,2,3,4]
// [null,null,null,null,null] 
```

我得到默认行为。你可以创建一个大而稀疏的数组，然后覆盖其中的几个元素。为什么要去没有数据的地方？

```
hedgehogs = Array(5);
hedgehogs[2] = 1;
hedgehogs[4] = 2;
hedgehogs.map((n,i)=>{ console.log([i,n].join("|"))})
// 2|1
// 4|2 
```

但这不是我所期望的行为。

而且，虽然我通常在`map`和`filter`中使用箭头函数，但你不必这样做。

```
const range3 = (n, i) => i;
hedgehogs = Array(5);
shrews = hedgehogs.fill().map(range3);
console.log(JSON.stringify(shrews));
console.log(JSON.stringify(hedgehogs));
console.log(range3(1,4)) // because they aren't just for arrays
// [0,1,2,3,4]
// [null,null,null,null,null]
// 4 
```

下一步是在创建前设置第一个值。但不是今天。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。