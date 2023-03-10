# 函数式 Javascript 中的 Arity

> 原文：<https://dev.to/samrocksc/arity-in-functional-javascript-4ngl>

好的推荐人:

*   [理解 JavaScript 中的奉承——一点一滴](https://blog.bitsrc.io/understanding-currying-in-javascript-ceb2188c339)

我认为大多数时候，至少阅读别人的代码比阅读冗长的博客文章更容易。让我们来看看这个非功能性的 JS 片段:

```
 function nfMultiply(a, b, c) {
  return a * b * c;
}

console.log('non-functional', nfMultiply(1, 2, 3)); 
```

Arity 本质上是可以传递给一个对象的函数的数量。这很令人困惑，但我认为这是你可以在一个函数中包含的函数的数量，让我们将上面的函数转换成一个 arity 为 *3* 。

```
function multiply(a) {
  return b => {
    return c => {
      return a * b * c;
    };
  };
}

console.log('arity breakdown', multiply(1)(2)(3)); 
```

如果你简单地想一下，这个函数可以折叠多少次。当我们开始创建更复杂的函数式 javascript 语句时，这变得很有用(我真的很喜欢在我的节点代码中使用 lodash，还因为它默认包含在 AWS lambdas 中，因此不会使它膨胀。):

## 更高级

```
const { has } = require('lodash');

const fakeDataFunc = () => 3;
const fakeObjFunc = () => ({ uncool: 'blue' });

function coolDude(a) {
  return b => {
    return c => {
      return d => {
        return e => {
          return {
            a,
            bTimesC: b * c(),
            d,
            eHas: has(e(), 'cool') ? 'sure does' : 'nope',
          };
        };
      };
    };
  };
}

console.log(
  'testing',
  coolDude('Mutliplied Value times a function is: ')(2)(fakeDataFunc)(
    'and here we generate a ternary if something is in an object:',
  )(fakeObjFunc),
); 
```

你可以把上面的片段按照你的意愿进行组合，然后用它们来创建更高阶的函数，尽情享受吧！！！