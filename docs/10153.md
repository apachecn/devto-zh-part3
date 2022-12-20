# 在 Typescript 中编写类型安全的 prop 函数

> 原文：<https://dev.to/cdimitroulas/writing-a-type-safe-prop-function-in-typescript-8d6>

注意:这篇文章也可以在我的个人博客上看到。如果你喜欢在那里阅读，请[点击这里](https://blog.cdimitroulas.com/posts/2019-03-16-type-safe-prop-func-ts.html)T3】

在这篇文章中，我将介绍如何编写一个类型安全的函数，它接受一个路径和一个对象，并返回该路径的值。这类似于 lodash 的“get”函数，除了我们将使它遵守更严格的规则！

[![gif-about-rules](img/058c92082c60294b5fde6cc182781694.png)](https://i.giphy.com/media/ma4msCmcHpAoBLU6hl/giphy.gif)

该功能的要求:

1.  访问不存在的对象属性时引发编译器错误
2.  正确推断返回类型
3.  是一个带有翻转参数的 curried 函数(因为我是一个函数型的人)

现在，您可能会问自己，为什么我不使用 Lodash 和 Ramda 等库提供的许多现有的“get”或“prop”函数中的一个呢？

原因在于，根据您对类型的要求，这些库提供的实现可能不足以满足您的需求。让我们仔细看看 Lodash 的 get 函数的行为，看看为什么它不能满足我们的要求。

首先，当我们寻找的路径存在于对象中时，`lodash.get`很好地推断了返回类型。

```
import _ from "lodash";

type MyObject = {
  what: boolean;
  am: string;
  i: number;
  doing: { value: string };
};

const myObject: MyObject = {
  what: true,
  am: 'hi',
  i: 0,
  doing: { value: 'x' }
};

const what = _.get(myObject, "what"); // what: boolean
const am = _.get(myObject, "am"); // am: string
const i = _.get(myObject, "i"); // i: number
const doing = _.get(myObject, "doing"); // doing: { value: string } 
```

Enter fullscreen mode Exit fullscreen mode

然而，作为一个库，Lodash 通常允许您将任何类型传递给它的函数，而不会在大多数时候抛出错误。在 JS 应用程序中，这可能很方便，因为您可以确保避免 TypeError，但是因为我们使用的是 Typescript，所以我们应该能够使用编译器来实现同样的事情。接下来，让我们看看如果我们试图访问一个不存在于
对象中的路径或者我们传入不是对象的值会发生什么:

```
import _ from "lodash"

type AnotherObject = {
  something: boolean
}

const anotherObject: AnotherObject = {
  something: true
}

// try to get "value" which doesn't exist in anotherObject
const value = _.get(anotherObject, "value") // value: any

// try passing in undefined as the object
const x = _.get(undefined, "value") // x: undefined

// try passing in a number as the object
const y = _.get(0, "value") // y: any 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，这些情况下的类型推断并不好。在第一种情况下，Lodash 甚至无法推断出 value 变量将是未定义的。在最后一种情况下，我们传入一个数字，结果被推断为“any ”,尽管它在这里也应该是未定义的。此外，在我们更严格的`prop`函数中，我们希望看到最后三种情况下的编译器错误。

现在我们已经看到了 Lodash 的 get 函数的局限性，让我们尝试编写自己的 prop 函数来满足我们的需求。

根据我们最初的需求，我们知道我们需要一个 curried 函数，它首先接受一个字符串路径，然后接受一个对象，并返回对象中该路径的值。

幼稚的尝试:

```
// This naive first attempt isn't descriptive enough of the object argument
// type or our return type. This results in poor type inference when
// using the function. 
const prop = (path: string) => (obj: Record<string, any>) => obj[path]

const x = prop('value')({ value: true }) // x: any 
```

Enter fullscreen mode Exit fullscreen mode

编译器需要更多关于返回类型的信息，以便正确地推断它。让我们尝试使用泛型来改进我们所拥有的。

```
// We are now telling the compiler that the return value is O[P] where O is our
// object and P is our path.
const prop = <P extends string>(path: P) =>
  <O extends Record<string, any>>(obj: O): O[P] =>
  obj[path]

const x = prop('value')({ value: true }) // x: boolean
const y = prop('value')({ anotherProperty: 'string', property: 0 }) // y: number 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们的返回类型现在被正确地推断出来了。然而，如果我们试图访问一个不存在于对象中的路径，会发生什么呢？

```
const x = prop('non-existant-property')({ value: true }) // x: unknown 
```

Enter fullscreen mode Exit fullscreen mode

编译器告诉我们返回类型未知，这不是我们想要的。我们知道我们的对象有什么键属性，所以我们应该能够让编译器理解键`non-existant-property`不存在于像`{ value: true }`这样的对象中。问题是我们的`prop`函数中的对象参数的类型不够描述性。

为了改进这一点，我们可以使用`in`操作符，它让我们将一组类型映射到一组新的类型(有关类型映射的更多信息:[https://www . typescriptlang . org/docs/handbook/advanced-types . html # mapped-types](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types))。
我们可以用它来获取我们的`path`参数的值，并指定这个值必须存在于我们的对象中。

```
// { [p in P]: V } specifies an object which must have a key of value p and that this
// key must pair with a value of V.
// Then, we say that our function returns this value V.
const prop = <P extends string>(path: P) => <V>(obj: { [p in P]: V }): V => obj[path]

const x = prop('value')({ value: true }) // x: boolean

// Now we correctly get the following compiler error for the below statement:
//   'value' does not exist in type '{ "non-existant-property": {}; }'.
const y = prop('non-existant-property')({ value: true })

// However, we have introduced a new problem.
// The following throws a compiler error:
//   'something' does not exist in type '{ value: boolean; }'.
const z = prop('value')({ something: 0, value: true }) 
```

Enter fullscreen mode Exit fullscreen mode

我们成功地添加了一个限制，即我们的对象必须有一个等于参数的键。然而，我们的类型现在限制太多，不允许
在对象上有任何额外的属性。我们可以通过告诉编译器这个对象也可以有任何其他的键和值来解决这个问题。

```
// we add "{ [key: string]: any }" to our object's type to specify that it can
// include any other properties
const prop = <P extends string>(path: P) =>
  <V>(obj: { [p in P]: V } & { [key: string]: any }): V =>
  obj[path];

// Hooray! This now compiles correctly
const z = prop("value")({ something: 0, value: true }); // z: boolean 
```

Enter fullscreen mode Exit fullscreen mode

让我们回顾一下我们的功能需求，看看我们是否满足了它们:

1.  访问不存在的对象属性时引发编译器错误

    是的，如果我们传递一个不存在于我们的对象中的`path`参数，我们会得到一个编译器错误。

2.  正确推断返回类型

    是的，我们的返回类型推断正确。

3.  是一个参数翻转的 curried 函数

    是的，我们的第一个参数是`path`,函数被 curried，所以参数可以部分应用，允许更好的函数组合。

很好，我们的函数满足了我们所有的需求，现在我们有了一个类型安全的`prop`函数，可以很好地在我们的应用程序中使用！