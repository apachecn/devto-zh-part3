# 重构向对象添加属性的条件

> 原文：<https://dev.to/samanthaming/refactoring-conditional-adding-property-to-object-45n3>

# 重构条件向对象添加属性

[![Code Tidbit by SamanthaMing.com](img/2dd64608936ff7db40211beb5f287cff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pl3SEnn7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d9b4roza5s0nxepcqbnn.png)

而不是使用三元组有条件地添加属性。使用“||”运算符👍因此，如果第一个表达式可以转换为真，使用它。否则，使用第二个表达式🌟

```
const refill = {};

// Ternary
refill['🥤'] = refill['🥤'] ? refill['🥤'] + 1 : 1;

// ✅ Refactor using ||
refill['🥤'] = refill['🥤'] + 1 || 1; 
```

## 这段代码在做什么？

让我用文字来解释这段代码在做什么:

1.  如果属性`'🥤'`存在于对象中，则加 1
2.  如果该属性不存在，则创建该属性并将值设置为 1

好了，让我们来看看单个场景的输出:

**1。**房产不存在

```
const refill = {};

refill['🥤'] = refill['🥤'] + 1 || 1;

console.log(refill); 
// OUTPUT ➡️ { '🥤': 1 } 
```

**2。**财产确实存在

```
const refill = { '🥤': 1 };

refill['🥤'] = refill['🥤'] + 1 || 1;

console.log(refill); 
// OUTPUT ➡️ { '🥤': 2 } 
```

## 用`if`改写这个三元

如果你不熟悉三元运算，让我用一个`if`语句重写这段代码。希望这将使这段代码更容易理解🍎

```
const refill = {};

if (refill['🥤']) {
  refill['🥤'] = refill['🥤'] + 1
} else {
  refill['🥤'] = 1
} 
```

## 解释`||`运算符

有很长一段时间，我以为`||`运算符只在条件中使用。直到我读了凯尔·辛普森的《你不知道的 JS》这本书——我强烈推荐读他的书！我说过它是免费的吗👏

凯尔将`||`解释为**选择器运算符**，而不是逻辑运算符:

> 为什么？因为它们实际上不会产生逻辑值(又名`Boolean`)。那么它们会产生什么结果呢？它们产生两个操作数中的一个(且只有一个)的值。换句话说，它们选择两个操作数值中的一个。

这意味着你可以用它来赋值。哇，那不是很酷吗🤩这正是我在这段代码中所做的👍

这种模式经常用于设置默认值。如果这是您第一次看到这种模式，那么用三进制来思考它会有所帮助。至少，在开始理解它的时候，这对我是有帮助的😝

```
'Option A' || 'default option';

// This is roughly the same as
'Option A' ? 'Option A' : 'default option'; 
```

⚠️但是！在你发疯用`||`操作符重构所有三元语句之前。看下一节！

## `||`不替代三元运算符！

虽然`||`操作符是设置默认值的一种非常有用的模式。它不能代替三进制。您应该只在希望跳过所有 falsy 值的情况下使用它。否则，最好使用三元组，这样可以更加明确。

### 一个三元优于`||`的例子

让我们来看一个稍微不同版本的填充示例。这里有一个创建填充对象的函数。但是现在，我们允许我们的用户跳过填充。意思是他们想要续杯。

```
function refillDrink(num) {
  const refill = {};

  refill['🍺'] = num || 1;

  return refill;
}

refillDrink(0);
// 😱 This is NOT what we want >> { '🍺': 1 } 
```

☝️哦，不！我们过不去`0`。那是因为`0`是一个假值。在这种情况下，三元会好得多。

```
function refillDrink(num) {
  const refill = {};

  refill['🍺'] = num !== undefined ? num : 1;

  return refill;
}

refillDrink(0);
// ✅ { '🍺': 0 } 
```

☝️:耶，我们可以把`0`设为我们的值👍

## 资源

*   [你不知道 JS:运算符||](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md#operators--and-)
*   [我关于虚伪价值观的文章](https://www.samanthaming.com/tidbits/25-js-essentials-falsy-values)

### 感谢阅读❤

打个招呼！ [Instagram](https://www.instagram.com/samanthaming/) | [推特](https://twitter.com/samantha_ming) | [脸书](https://www.facebook.com/hisamanthaming) | [媒体](https://medium.com/@samanthaming) | [博客](https://www.samanthaming.com/blog)