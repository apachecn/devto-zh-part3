# 原生解决 JS MVM 问题

> 原文：<https://dev.to/karric/solving-the-js-mvm-problem-natively-1coj>

首先我要说，我不是 JS 高手，我主要是为了娱乐而编码。但是我也涉猎过其他语言，有面向对象的，也有结构化的。

我在寻找关于 JS 提案核心概念的反馈，可以说是提案前的反馈。我完全期待这会失败，但我没有在网上找到任何关于它的东西，所以这里什么都没有。

## 问题

我在使用 JS 和 HTML 时遇到的主要问题是 MVM 数据绑定问题——如何自动保持原始数据和数据的 HTML 表示同步。

有几个框架解决了这个问题，JS 应该很好地解决这个问题，尤其是考虑到它的年龄。

所以下面开始预提案，请让我知道我是不是疯了。

## 预求婚-链接& unlink 关键字

我不认为 JS 能够神奇地将数据结构转换成 HTML 模板引擎更加实用，并且提供了很多控制。

我确实认为 JS 应该有一种*本地方式将属性和原始值*链接在一起，这样它们就像“通过引用传递”。对我来说，大部分 MVM 的痛苦都来源于此。这并没有解决其他 MVM 特性，比如在 set/get 上执行任意代码。这也许应该用 addEventListener 之类的东西来解决。

## 语法

我在上面建议的应该是这样的:

```
let elm = $('#yourElement')[0];
let obj = { 'displayValue': 10 };
let txt = elm.innerText;
let str = 'a string';

link elm.innerText ...(others) obj.displayValue 
link txt ...(others) obj.displayValue 

// Right-most value is assigned to everything preceding it, as normal. Changing any of these linked variables would change all of them.

link obj.displayValue str; // All of the linked values now have str's value.

elm.innerText = 'different string'; // All of the linked values are now this.

unlink txt; // txt is no longer linked and can be changed independently. 
```

上面的链接行完全类似于:

```
let a = ['a'];
let b, c, d;

b = c = d = a; // all 4 are now referencing the same data; left-right order matters. Changing any of them changes all of them.

a = {}; // now all 4 are referencing this empty object. 
```

虽然某种“=”变体可能感觉像更合适的语法(因为它是一种赋值)，但所有优雅的变体都被采用了，无论如何这种工作方式非常不同。

## 反馈

请告诉我您对此的看法，以及它是否值得创建一份正式的 ES 建议书。我认为这可以在不需要库和代码开销的情况下减少 MVM 的痛苦。

如果你认识任何 ECMA 成员，请把这个传给:D