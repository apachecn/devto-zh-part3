# 在 JavaScript 类中定义私有成员:如何和为什么

> 原文：<https://dev.to/allenwoods/defining-private-members-in-javascript-classes-how-and-why-244m>

## 上下文

面向对象编程中更重要的概念之一是私有属性和方法的使用，从这里开始统称为私有成员。

基本思想是，对应用程序来说是关键数据的任何数据都应该是私有的，以防止灾难性的错误或利用，否则如果这些私有成员是可访问的，就可能发生这种情况。

虽然使用 webpack 或 ES6 模块等技术实现真正的私有成员是可能的，但我将讨论如何使用闭包、Symbol()和 WeakMap()实现非常相似的结果。

*关于这个主题的更多细节，超出了本文的范围，请给[这篇文章](http://2ality.com/2016/01/private-data-classes.html)的灵感，作者[阿克塞尔·劳施迈尔](https://twitter.com/rauschma)一个读者。*

## 命名约定

在研究如何用普通 JavaScript 实现类的私有成员时，我发现一些文章和 Stackoverflow 线程建议使用一种通用的命名约定，即在变量名的开头放置下划线来表示“私有”状态。

```
class NamingConventionClass {
  constructor() {
    this._hidden = "Can you see me?";
  }
}; 
```

这种方法的问题在于，它是一种向其他程序员表明一个成员是否是私有成员的方式。简而言之，通过命名约定“_hidden”的东西还是可见的。

```
let peekaboo = new NamingConventionClass();
console.log(peekaboo._hidden + '...Yes I can.');

// outputs 'Can you see me? ...Yes I can.' 
```

如果目的是保持我们的类对象上的某些成员从外部不可访问，这是一个问题。任何在浏览器中使用 inspect 的人都可以操作变量，并产生不良结果，这可能会产生超出 DOM 操作范围的真正后果。

我们需要某种私有容器来放置我们的私有成员，而不丢失它们的位置或者它们属于哪个实例。

## 进入 WeakMap

我发现(在撰写本文时)由 vanilla JavaScript 提供的最佳解决方案是 WeakMap 类。WeakMap 只是键和值的集合。每个键必须是对一个对象的引用，每个对应的值可以是任意数据。因为每个`key, value`对是同时添加的，所以它们的索引总是对应的。

```
let myWeakMap = new WeakMap(); 
```

与 map API 提供的其他选项相比，使用 WeakMaps 的主要优势在于它们的引用被“弱”保存，允许垃圾收集。这意味着如果 WeakMap 中引用的数据被删除，比如类实例的销毁，WeakMap 的分配也将随之从内存中删除。

使用 WeakMaps 混淆一个类的私有成员的一种方法是做一些类似于 Axel 文章中提供的例子的事情，如下:

```
let _variableOne = new WeakMap();
let _variableTwo = new WeakMap();

class WeakMapPrivateMembers {
  constructor(valueOne, valueTwo) {
    _variableOne.set(this, valueOne);
    _variableTwo.set(this, valueTwo);
  }
  combineValuesInString() {
    let v1 = _variableOne.get(this);
    let v2 = _variableTwo.get(this);
    return `${v1}, ${v2}`;
  }
};

const hideTwoVariables = new WeakMapPrivateMembers('foo', 'bar');
console.log(hideTwoVariables.combineValuesInString()); 
```

这种方法至少有下面列出的三个主要问题。

*   WeakMaps 可在全局名称空间中访问。
*   对于每个变量，弱映射的定义效率很低。
*   武器地图仍然可以被访问和覆盖。

我们正在取得进展，但现在我们的数据存储范围出现了问题。我们需要重新定义我们的类是如何被实例化的，以使我们的 WeakMap 数据保持不可访问。

## 类为闭包

事实证明，您可以将整个类定义包装在一个立即调用的函数表达式中，或称为`IIFE` *(读作“iffy”)*。基本语法如下:

```
const MyClosureClass = ((param1, param2, param3) => {

  // Define a variable inside the scope of the closure to keep it hidden.
  // This WeakMap will act as a repository for all instances of this class.

  const _wm = new WeakMap();

  return class MyClosureClass {
    constructor(param1, param2, param3) {
      // add this class instance's private members

      _wm.set(this, {
        property1: param1,
        property2: param2,
        property3: param3
      });
    }
    get privateMembers() {
      // return the object stored at object key of "this"

      return _wm.get(this);
    }
  };
})();

let myPrivacy = new MyClosureClass("something important");

console.log(myPrivacy.privateMembers);
// The above line will return 'something important'

console.log(myPrivacy.params);
// The above line will return undefined

console.log(_wm);
// The above line will return undefined as well 
```

这意味着我们的类现在可以用传入的数据进行实例化，这些数据只能通过批准的 getter 方法获得。此外，我们可以嵌套一个对象，该对象包含一个更复杂的私有成员结构，我们希望将这些私有成员与类实例相关联。

我们可以更进一步，在返回数据之前对数据进行转换，或者简单地使用私有存储的值来执行指示返回更安全值的条件。

```
// (code abridged)

    // rewrite the getter method as a confirmation method
    hasPrivateMembers() {
      return !!_wm.get(this);
    }

// (code continued) 
```

值得注意的是，您应该在您的闭包范围内编写一个 helper 方法，以抽象出每次您想要访问私有成员`privateMemberName`时对类型`_wm.get(this).params[privateMemberName]`的需求，如下所示。

```
// Inside the scope of our closure...

const _self = function(objRef) {
  _wm.get(objRef);
} 
```

## 嵌入符号引入熵

如果你真的，真的，真的需要一些私密的东西，其他选择更适合这个——在没有客户端可见性的情况下保护数据库中的数据是其中的主要选择。

但是，如果您想将客户端数据的隐私提高到一个偏执的水平，您也可以通过使用符号指向私有成员来将它们嵌套得更深。

```
// (code abridged)
    constructor(param1, param2, param3) {
      _wm.set(this, {
        [Symbol('root')]: {
          property1: param1,
          property2: param2,
          property3: param3
        }
      });
    }
// (code continued) 
```

应用这种强调级别的隐私的主要缺点是，您需要一个相当复杂的助手方法来访问这些私有成员。然而，如果您想沿着这条路线探索它是如何操作的，那么 helper 方法应该是这样的:

```
// Inside the scope of our closure...

const _self = function(objRef) {
  let obj = _wm.get(objRef)[
    Object.getOwnPropertySymbols( _wm.get(objRef) )[0]
  ];

  return obj;
} 
```

## 结论

如果您不想依赖整个框架来为您完成这项工作，那么在撰写本文时，使用 WeakMap 和`IIFE`包装器将您的类转换成闭包对于类的私有成员来说是一个极好的解决方案。

我希望本文能够帮助指导您在构建需要公共和私有成员的 OOJS 应用程序时改进设计考虑。

这里是我们示例类的最终引用，利用了我们刚刚讨论的所有内容。

```
const MyClosureClass = ((param1, param2, param3) => {

  const _wm = new WeakMap();

  const _self = function(objRef) {
    let obj = _wm.get(objRef)[
      Object.getOwnPropertySymbols( _wm.get(this) )[0]
    ];
    return obj;
  };

  return class MyClosureClass {
    constructor(param1, param2, param3) {

      _wm.set(this, {
        [Symbol('root')]: {
          property1: param1,
          property2: param2,
          property3: param3
        }
      });
    }
    get privateMembers() {
      return _self(this);
    }

    getSpecificMember(memberName) {
      return _self(this)[memberName];
    }

    hasPrivateMembers() {
      return !!_self(this);
    }
  };
})(); 
```