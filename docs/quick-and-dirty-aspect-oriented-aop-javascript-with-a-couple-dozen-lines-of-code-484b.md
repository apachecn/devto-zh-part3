# 快速而肮脏的面向方面(AOP) Javascript，有几十行代码

> 原文：<https://dev.to/dexygen/quick-and-dirty-aspect-oriented-aop-javascript-with-a-couple-dozen-lines-of-code-484b>

面向方面编程(AOP):每个人都在谈论，但没有人去做。实际上，甚至没有人谈论它，因为如果有足够的用例，他们就会谈论它；我们将在最后回到这一点。

一两年前，我需要在 Javascript 中增加一个回调函数，当我发现下面的 Stackoverflow 答案时，我立刻想起了 AOP。代码如下:

```
var original = callback; //existing function
callback = function() {
    // Do something with arguments:
    console.log(arguments);
    return original.apply(this, arguments);
}; 
```

在 AOP 中，原始函数仍然像正常情况下一样执行，只是增加了其他行为。关于更正式的定义，我建议您参考维基百科的文章，但是将特别依赖于它提到的以下用例:“当函数名以‘set’开头时，记录所有函数调用”。

我已经在 repl 之后的[实现了这一点；你可以去那里点击“运行”来看它的运行。该代码还遵循:](https://repl.it/@dexygen/quickAndDirtyJsAOP)

```
const myModule = (function() {
  let result;
  const my = {
    setResult(x,y) {
      result = x + y;
    },
    getResult() {
      return result;
    }
  }
  return my;
})();

const predecessor = function() {
  console.log('arguments: ', arguments);
}

let precedableFns = {};

for (let fname in myModule) {
  if (/^set[A-Z]/.test(fname)) {
    precedableFns[fname] = myModule[fname]; //save original function
    myModule[fname] = function() {
      predecessor.apply(this, arguments);
      precedableFns[fname].apply(this, arguments); //run original
    }
  }
}

myModule.setResult(2,3);
console.log(myModule.getResult());

myModule.setResult(3,4);
console.log(myModule.getResult()); 
```

从上面开始有很多可能性。例如，您可能有一个“successorFn ”,它在函数返回之前/之后立即从`setTimeout`执行。

一个更有趣的用例可能是只在服务器主机是`localhost`或您感兴趣的其他值时才执行控制台日志记录。可能性是无穷的，尽管有些神秘。