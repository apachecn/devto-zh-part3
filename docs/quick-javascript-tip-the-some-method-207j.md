# JavaScript 快速技巧:some()方法

> 原文：<https://dev.to/mattsparks/quick-javascript-tip-the-some-method-207j>

我最近了解到一个我从未使用过的很棒的 JavaScript 方法。韦斯·博斯引起了我的注意。`some()`方法是测试数组值并查看是否至少有一个值返回`true`的便捷方法。`some()`在 ES6 之前实现，支持[广泛](http://kangax.github.io/compat-table/es5/#test-Array.prototype.some)(ie 11 支持)。

需要注意的是，这个方法只对真的`Array`有效，其他的可迭代类型不实现这个方法。

问题:您有一个包含酒店允许的动物种类的数组。你如何确定你的宠物蛇，斯利瑟斯先生，可以跟你一起旅行？

**一个年长的道:**

```
var animals = ['dogs', 'cats', 'snakes', 'birds', 'pandas'];
var snakesAllowed = false;

for (var i = 0; i < animals.length; i++) {
  if(animals[i] === 'snakes') {
    snakesAllowed = true;
    break;
  }
}

console.log(snakesAllowed); // true 
```

**使用`.some()`:**T3】

```
// Using arrow functions
const animals = ['dogs', 'cats', 'snakes', 'birds', 'pandas'];

const snakesAllowed = animals.some((type) => type === 'snakes');

console.log(snakesAllowed); // true

// Using normal functions
const animals = ['dogs', 'cats', 'snakes', 'birds', 'pandas'];

const snakesAllowed = animals.some(function(type) {
  return type === 'snakes';
});

console.log(snakesAllowed); // true 
```

**补遗:**

[@attacomsian](https://dev.to/attacomsian) 在评论中提到`.includes()`会更适合上面的问题。我完全同意。上面的例子只是为了说明`.some()`是如何工作的，而不是以最有效的方式解决问题。

再举一个例子来说明`.some()`的功能，假设我们有一系列漫画书的价格，我们想知道是否有超过 10 美元的。

```
const prices = [5, 8, 11, 10, 25];
const aboveTen = prices.some((price) => price > 10);

console.log(aboveTen); // true 
```

延伸阅读: [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)