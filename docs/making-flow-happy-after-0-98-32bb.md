# 0.98 后让流量快乐

> 原文：<https://dev.to/wgao19/making-flow-happy-after-0-98-32bb>

这是流量更新后的星期一😱0.98 似乎包含了很多修复。很高兴看到流量一个版本比一个版本更有意义。我也很高兴在旅途中学习心流的点滴知识，所以我想我应该分享一下:)

发布笔记这里是[这里是](https://github.com/facebook/flow/releases/tag/v0.98.0)，以下是我的笔记。流动请快乐🤞

* * *

> 默认情况下，打开不推荐使用的实用程序 lint

可能的错误:

*   不赞成使用的实用程序。不推荐使用`$Subtype`类型！(已弃用-实用程序)
*   不赞成使用的实用程序。不推荐使用`$Supertype`类型！(已弃用-实用程序)

`deprecated-utility`是在 0.89 中添加了[的 lint 规则。到目前为止`$Subtype`和`$Supertype`都被弃用了。不过从这个版本开始 Flow 才开始抱怨，因为`deprecated-utility`现在默认开启。](https://github.com/facebook/flow/releases/tag/v0.89.0)

您可以通过添加回`.flowconfig` :
中的 lint 选项来关闭它

```
# .flowconfig
[lints]
deprecated-utility=off 
```

但其实也没那么难修。

那么`$Subtype`和`$Supertype`到底是什么呢？`$Subtype`通常指这里期待的比里面的更精致`$Subtype`。反正这只是我的理解。你仍然可以在这里找到 T4 的文件。

也许您以前使用过`$Subtype`来精化组件属性，正如在它们被弃用时所建议的那样，您可以使用有界泛型来恢复精化。

某些旧版本的流类型库定义可能会引发错误。(比如造成不快的 Flow 0.89 之前我们用的是 Redux v3。然而，[在流类型化的](https://github.com/flow-typed/flow-typed/pull/3223)中已经有一个 libdef 更新。)

无论如何，我对他们感到有点害怕。现在他们走了，是时候感到高兴并摆脱他们了:)

* * *

> 围绕只读对象和数组的更改和修复

可能的错误:

*   无法将`42`分配给`x[0]`，因为只读数组无法写入。
*   无法将`array`分配给`problem`，因为只读数组类型[1]与数组类型[2]不兼容。
*   无法将`3`分配给`x.a`，因为属性`a`不可写。

很高兴看到流量在不断改善，以及如何改善。(再加上一点点幽默，来看看对之前的不和谐的修正)。

所以我发现了三个关于只读的变化和修正。

*   只读数组不能写入任何类型的索引，[提交](https://github.com/facebook/flow/commit/875cb7a94d22237cb324aa0e896fcf78c99342bf)

这现在不允许你改变只读数组。你可能会问，为什么我应该被允许改变它们？原来之前如果索引是类型`any`的话，你就可以。🤷🏻‍♀️

然后还有两个:

*   混合细化到 typeof = object 下的只读对象，[提交](https://github.com/facebook/flow/commit/0983bd041c6289897c9b33d86e9e14d2b686c2e5)
*   由数组混合提炼产生$ReadOnlyArray，[提交](https://github.com/facebook/flow/commit/5639532065954e7f66a968af5e68f76a762a9c14)

我没有比提交消息更好的解释了。让我引用一下:

**【Bugbash】Mixed 在 typeof = object**
下细化为只读对象摘要:
将`mixed`细化为`{[string] : mixed}`是不安全的，因为这允许向细化后的对象中写入任何类型的内容，并且我们不知道该对象持有什么类型的值。唯一安全的操作是从对象中读取`mixed`，因此属性必须是只读的。

```
function bad (x : mixed) {
  if (typeof x === "object" && x !== null) {
    x.a = 3;
  }
}

let obj : {a : string} = {a : "oops"};
bad(obj); // yikes 
```

还有一个带数组的:

**【Bugbash】混炼由阵法产生** `$ReadOnlyArray<mixed>`

摘要:
将`mixed`细化为`Array<mixed>`是不安全的，因为这允许向细化的结果中写入任意值，并且因为我们只知道该值是一个数组，所以我们无法知道向该数组中写入给定值是安全的。然而，我们确实知道，我们从数组中得到的任何东西都将是`mixed`，所以将其键入为`$ReadOnlyArray<mixed>`是安全的。

```
function bad(array: mixed) { 
  if (Array.isArray(array)) { 
    const problem: Array<mixed> = array; problem[1] = 0; 
  }
}
bad((["3"] : Array<string>)); 
```

* * *

> 对于没有 return 语句的函数，在类型检查开始之前推断 void

可能的错误:

**注**:我认为这次修复可能导致的错误可能相当多变。这里有两个我知道的:

*   无法将`x.noReturn()`转换为数字，因为未定义的[1]与数字[2]不兼容。
*   无法隐藏 proto 属性`yourFunction`[1]，因为对象类型[2]与返回值中的 undefined [3]不兼容。

提交流代码库:[为没有显式返回的函数提前推断 void】](https://github.com/facebook/flow/commit/7b1798a5443671010bdf9138985ed25cd6d0a9fe)

如果一个函数没有 return 语句，那么流程现在会将其推断到`void`。有趣的是，在这之后出现的最常见的错误是关于*隐藏原型属性*。检查以下[试流](https://flow.org/try/#0PTAEAEDMBsHsHcBQBLAtgB1gJwC6gFSgCGAzqAEoCmRAxnpFrKqAORbV0sDciiN0pMgFkAngGEmmAHaUpeSgA8csgCZkqtHADoJGWDLkAefAD5QAb0QBIABZEpK6JTHRkNANYAuUAAoAlKAAvGbmAL48oKCIkbb2js6uHv4W0aBWVjT6JLBOWnAA5j4s-G7ulCoAhCx+PFahvFbsDpRYyZbp7DgArlhSoIYqyABuoPoupYHmODbIJFp2Dk7jHqGgwCbW9aFAA) :

```
import * as React from 'react';

class MyComponent extends React.Component<*> {
  handleClick: () => {};  // <- {} means an object here

  handleClick() {
    console.log('clicked!');
  }

  render() {
    return <div onClick={this.handleClick} />
  }
} 
```

修复只是对它们进行适当的注释`() => void`

* * *

> 修复了一个阻止 Flow 请求所需类型注释的错误。

这一行听起来很吓人😱但是我还没有遇到任何似乎相关的不愉快。如果我做到了，就会回到这里。

* * *

## 参考文献

*   【0.98 版本说明
*   [对没有显式返回的函数提前推断`void`](https://github.com/facebook/flow/commit/7b1798a5443671010bdf9138985ed25cd6d0a9fe)
*   [尝试流中不匹配注释的隐藏 proto 属性导致的错误](https://flow.org/try/#0PTAEAEDMBsHsHcBQBLAtgB1gJwC6gFSgCGAzqAEoCmRAxnpFrKqAORbV0sDciiN0pMgFkAngGEmmAHaUpeSgA8csgCZkqtHADoJGWDLkAefAD5QAb0QBIABZEpK6JTHRkNANYAuUAAoAlKAAvGbmAL48oKCIkbb2js6uHv4W0aBWVjT6JLBOWnAA5j4s-G7ulCoAhCx+PFahvFbsDpRYyZbp7DgArlhSoIYqyABuoPoupYHmODbIJFp2Dk7jHqGgwCbW9aFAA)
*   [Redux 4 流型 libdef 更新](https://github.com/flow-typed/flow-typed/pull/3223)