# 在 JavaScript 中自定义对象强制

> 原文：<https://dev.to/jfet97/customize-objects-coercion-in-javascript-83d>

[![](img/8aa3a72841fea94149642f1c714ed8d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l-CdCBXy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q4ocbx6ttxp4bxh7tm6n.jpg)

## 简介

与其他编程语言不同，JavaScript 不允许操作符重载。它允许我们做的是修改对象被转换的原始值。这是因为当一个对象被强制时，结果是并且必须是一个原始值。

不幸的是，我们只能把这个机会看作是微不足道的安慰奖，因为我们所能获得的控制是非常有限的。在任何情况下，知道这种语言给我们提供了什么可能性总是有用的，考虑到 ES6 已经带来了一个相当有价值的解决方案。

整篇文章基于大量与[强制](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md)相关的概念。事实上，我们能做的是修改在我们的对象上执行的强制的结果。

## ES6 之前的定制

由于对象的强制很大程度上基于 **toString** 和方法的**value，它们在默认情况下可以在任何对象上调用，所以在 ES6 之前我们可以做的只是在我们的对象上重新定义这些方法。**

为什么我用了*这个词来重新定义*？为什么默认情况下这两个方法是可调用的？在 Javascript 中，由于所谓的*原型链*，所有对象都自动链接到**对象。原型**对象。这个对象定义了相当多的方法，因此可以直接从与之连接的所有对象中调用。点击了解更多[。](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md)

在重新定义它们的行为之前，让我们来看看默认的行为:

```
 var object = {
        prop: "value",
    };

    object.valueOf(); // object
    object.toString(); // "[object Object]" 
```

Enter fullscreen mode Exit fullscreen mode

如您所见， **valueOf** 只是返回对象本身(对的引用)。相反，另一个方法， **toString** ，读取**[[类]]** 内部属性的值，并构造众所周知的*"[对象对象]"* 字符串。

数组也是对象，但是对于它们来说， **toString** 方法的行为在默认情况下已经被重新定义:

```
 var array = [1, 2, 3];

    array.valueOf(); // array
    array.toString(); // "1,2,3" 
```

Enter fullscreen mode Exit fullscreen mode

当任何对象被强制时，根据初始条件， **toString** 和的**value 之间的方法将优先。只有当它不返回原始值(如 Object.prototype 的 valueOf 方法)时，另一个方法才会作为后备调用。如果它没有返回一个原始值，一个**类型错误**将被抛出。**

**警告！当我们重新定义这些方法时，我们可以决定返回 **null** 或 **undefined** ，因为它们是有效的原始值。然而，Javascript 从来不会对我们可用的对象产生这样的结果，我们应该遵循它的例子，返回字符串**、**数字**和**布尔值**中的一个。

像`Number(obj)`、`+obj` ( **一元+** )、**一元-** 、**二元-** 、 ***** 、 ****** 、 **/** 、 **%** 这样的数值运算会明确优先考虑方法的**值，而像`String(obj)`这样的显式强制运算会优先考虑 **toString** 方法。
面对**二进制+** 和 **==松散等式**运算符时产生疑问，这些运算符优先考虑**的**值。**

让我们看看，在实践中，这些东西是如何帮助我们达到目标的:

```
 var myObj = {
        toString: function() {
            return "myObj";
        },
        valueOf: function() {
            return 10;
        }
    }

    // valueOf precedence
    Number(myObj); // 10
    +myObj; // 10
    myObj - 7; // 3
    myObj % 3; // 1
    myObj + 15; // 25
    "this is " + myObj; // "this is 10"
    myObj == 10; // true

    // toString precedence
    String(myObj); // "myObj" 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们重新定义了对象的行为。

然而，我们可能不满意`"this is" + myObj;`的结果，更喜欢`"this is myObj"`的结果。不幸的是，除了使用**字符串**函数进行显式强制之外，在 ES5 中，唯一的方法是将字符串`"myObj"`的返回也强加给方法的**value，牺牲了在算术运算中使用该对象的可能性，牺牲了从一个数字中的`"myObj"`字符串的强制尝试中派生出的无限系列的 **NaN** 。**

## 定制同 ES6

让我介绍一个新的概念:提示。当任何对象被强制转换为原语时，我们之前提到的优先选择两种方法之一的决定是通过一个提示完成的。
提示的可能值有:**数字**、**字符串**、**默认**。
当提示值为**时，字符串**将优先于 **toString** 方法，当提示值为 **number** 或 **default** 时，优先于方法的**值。该逻辑的唯一例外是**日期**“类型”，对于该类型，**默认**提示值将优先于 **toString** 方法。**

让我们看看到目前为止看到的主要操作都转发了哪些提示:

| 暗示 | 操作 |
| --- | --- |
| 线 | 字符串()，插值[ES6] |
| 数字 | 数字()，一元+，一元和二元-，*，** ，/，% |
| 系统默认值 | 二进制+，== |

这可能看起来相反，但是 ES5 也是基于相同的概念。不同之处在于 ES6 提供的与该机制直接交互的可能性。

有必要向对象提供一个具有特殊名称的方法，该方法采用一个带有**提示**的值的字符串。基于它我们可以决定做什么，像手动调用 **valueOf** 和 **toString** 方法，如果我们愿意，或者调用其他方法。重要的是生成一个原始值作为最终结果，以避免遇到**类型错误**。

这种方法的特殊名称是什么？是**符号. toPrimitive** 。这篇文章不会谈论符号，因为没有必要对它们有信心来达到我们的目标。

让我们看看它的一个可能的实现，它允许我们获得由 ES5:
定义的相同行为

```
 let obj = {
        [Symbol.toPrimitive](hint) {
            // it checks if a value is an object
            const isObject = (value) => value !== null 
                                        && typeof value === 'object' 
                                        || typeof value === 'function';

            switch(hint) {
                case "default": 
                case "number": 
                    // precedence to valueOf if the hint is "number" or "default"
                    const value = this.valueOf();

                    // if the result is a primitive, it can be returned
                    if(!isObject(value)) return value;

                    // otherwise the toString method is used as fallback
                    else return this.toString();

                case "string": 
                    // precedence to toString if the hint is "string"
                    const string = this.toString();

                    // if the result is a primitive, it can be returned
                    if(!isObject(string)) return string;

                    // otherwise the valueOf method is used as fallback
                    else return this.valueOf() 
             }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果 **toString** 和方法的**value 不可信并可能导致**类型错误**，我们该怎么办？
让我们来看看一个可能的解决方法:** 

```
 let obj = {
        [Symbol.toPrimitive](hint) {
            // it checks if a value is an object
            const isObject = (value) => value !== null 
                                        && typeof value === 'object' 
                                        || typeof value === 'function';

            switch(hint) {
                case "default": 
                case "number": 
                    // precedence to valueOf if the hint is "number" or "default"
                    let res = this.valueOf();

                    // if the result is a primitive, it can be returned
                    if(!isObject(res)) return res;

                    // otherwise the toString method is used as fallback
                    res = this.toString();

                    // if the result is a primitive, it can be returned
                    if(!isObject(res)) return res;

                    // otherwise returns an appropriate primitive value
                    return NaN;

                case "string": 
                    // precedence to toString if the hint is "string"
                    let res = this.toString();

                    // if the result is a primitive, it can be returned
                    if(!isObject(string)) return string;

                    // otherwise the valueOf method is used as fallback
                    res =  this.valueOf();

                    // if the result is a primitive, it can be returned
                    if(!isObject(res)) return res;

                    // otherwise returns an appropriate primitive value
                    return "";
             }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

记住上面的表格，我们可以很容易地对该机制进行更多实质性的修改。作为一个实际的例子，我使用了之前定义的 *myObj* 对象，在这里，当**二进制+** 操作符开始起作用时，我希望对象被转换成一个字符串值，而不是一个数字值。如表所示， **==运算符**也将受到我的更改的影响。

```
 let myObj = {
        toString() {
            return "myObj";
        },
        valueOf() {
            return 10;
        },
        [Symbol.toPrimitive](hint) {
            switch(hint) {
                case "number": 
                    return this.valueOf();

                case "default":     
                case "string": 
                    return this.toString();
             }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，由于有了**一元+** ，我们仍然可以在**二元+** 运算中使用 *myObj* 的数值，或者在必要时使用 **==** 运算符。
表格印证了这个说法:**一元+** 暗示*“数”*。

```
 "this is " + myObj; // "this is myObj"
    `greetings from ${myObj}`; // "greetings from myObj"
    "myObj" == myObj; // true

    32 + +myObj; // 42
    `greetings from ${+myObj}`; // "greetings from 10"
    10 == +myObj; // true 
```

Enter fullscreen mode Exit fullscreen mode