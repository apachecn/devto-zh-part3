# 在 JavaScript 中安全复制嵌套对象

> 原文：<https://dev.to/krsgrnt/safely-copying-nested-objects-in-javascript-5fpi>

在 JavaScript 中管理状态的一个基本概念是永远不要直接改变数据。在大型应用程序中，当状态存储在嵌套对象中时，遵守这一规则会变得很困难。如果你使用像 Redux，[这样的库，这一点尤其重要，因为文档建议使用](https://redux.js.org/recipes/structuring-reducers/immutable-update-patterns):

> 更新嵌套数据的关键是必须适当地复制和更新嵌套的每一层。对于那些学习 Redux 的人来说，这通常是一个很难理解的概念，并且在尝试更新嵌套对象时经常会出现一些特定的问题。这些会导致意外的直接突变，应该避免。

为了避免直接突变状态，我们需要制作对象的副本，适当修改，然后用它代替原来的。这是 React 的 *setState* 方法背后的原理，该方法接受一个对象，它将用该对象替换组件状态中的现有对象。

## JavaScript 中的引用与值类型

JavaScript 对象是通过引用内存中的位置来传递的数据类型，而不是通过实际值来传递的字符串或整数。这意味着复制对象可能会很棘手，因为赋值可能不会像你预期的那样工作。

举一个用户对象的例子:

```
const state = {
    name: 'John',
    address: {
        city: 'London',
        country: {
            countryName: 'United Kingdom',
            countryCode: 'UK',
        },
    },
}; 
```

我们不能通过将这个对象赋给一个新变量来复制它:

```
const copyState = state;
copyState.name = 'Jane';
console.log(copyState === state); // true
console.log(state.name); // 'Jane' 
```

copyState 变量指向与原始状态对象相同的引用，这就是严格的等于检查返回 true 的原因。当我们修改 copyState 对象的 name 属性时，我们正在改变状态变量所指向的同一个对象。通常这并不是你想要的。

## 传播算子

[展开运算符或语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)(...)可用于制作对象的浅层副本。

```
const shallowCopyState = { ...state };
shallowCopyState.name = 'Jane';
console.log(shallowCopyState === state); // false
console.log(state.name); // 'John' 
```

现在，我们的两个变量指向不同的对象引用。修改 shallowCopyState 对象的 name 属性值对原始状态对象没有影响，并且严格的等于检查返回 false。

在这个上下文中，浅意味着对于任何给定的展开对象，新变量的最高级别是包含原始对象的相同属性和值的对象，但是在内存中有一个新的引用。然而，任何更低层次或嵌套的对象将保持指向它们的原始引用:

```
const shallowCopyState = { ...state };
console.log(shallowCopyState === state); // false
shallowCopyState.address.city = 'Paris';
console.log(shallowCopyState.address === state.address); // true
console.log(state.address.city); // 'Paris' 
```

为了安全地复制像我们的用户对象这样的深层对象，我们还需要在对象的嵌套层次使用 spread 操作符:

```
const deeperCopyState = {
    ...state,
    address: {
        ...state.address,
    },
};
deeperCopyState.address.country.countryCode = 'FR';
console.log(deeperCopyState.address === state.address); // false
console.log(deeperCopyState.address.country === state.address.country); // true
console.log(state.address.country.countryCode); // 'FR' 
```

正如你在上面的例子中看到的，address 的嵌套对象在两个变量中是不同的，但是它的 country 的嵌套对象*仍然*指向与我们的原始 state 变量相同的引用。我们可以通过进一步深入来解决这个问题，但在这一点上，可能更容易找到一个库来帮助我们，比如 Immer。

## Immer

[Immer 库](https://github.com/immerjs/immer%5D)由一个 *produce* 函数组成，该函数获取一个现有对象并返回一个新对象。因为您还可以指定新对象的哪些属性将被更新，所以这是一种安全创建状态对象的极好方式:

```
const state = {
    name: 'John',
    address: {
        city: 'London',
        country: {
            countryName: 'United Kingdom',
            countryCode: 'UK',
        },
    },
};

const immerState = immer.produce(state, draftState => {
    draftState.name = 'Jane';
    draftState.address.city = 'Paris';
    draftState.address.country.countryName = 'France';
    draftState.address.country.countryCode = 'FR';
}); 
```

immerState 变量与原始状态对象完全解耦，并且不共享对它的引用:

```
console.log(immerState === state); // false
console.log(immerState.address === state.address); // false
console.log(immerState.address.country === state.address.country); // false
console.log(state.address.country.countryCode); // 'UK'
console.log(immerState.address.country.countryCode); // 'FR' 
```

## 终于

值得参考一下关于嵌套对象的 Redux 文档:

> 显然，每一层嵌套都使这更难阅读，并给了更多的出错机会。这是鼓励你保持状态扁平化的几个原因之一。

如果您发现自己正在处理许多层次的对象，并且需要大量使用 spread 操作符或像 Immer 这样的库，那么值得考虑是否有一种方法可以简化这些对象的组成。然而，如果您发现自己处于这些结构很常见的代码库中，希望本文将帮助您保持状态不变。

[上面的代码例子是 GitHub gist](https://gist.github.com/krsgrnt/f8f56141ebe463c0e9f167543de5c918) 上的。