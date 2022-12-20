# Typescript 中的泛型类型保护

> 原文：<https://dev.to/krumpet/generic-type-guard-in-typescript-258l>

# 用 Typescript 编写一个泛型类型保护，我从中学到了什么

*   [简介](#introduction)
*   [引入构造函数类型签名](#introducing-the-constructor-type-signature)
*   [扩展类型保护为原始类型工作](#extending-the-type-guard-to-work-for-primitive-types)
*   [将所有这些放在一起](#putting-it-all-together)
*   [总之](#in-summary)
*   [来源](#sources)
*   [附录](#addendum)

## 简介

我最近在工作中遇到了一个问题，这个问题源于一个函数假设它的输入是一种类型，而实际上它有时可能是另一种类型。

我最初尝试解决这个问题是为了确定输入可以有哪些类型，并修改函数声明，使输入的类型是所有可能类型的联合，然后在函数中使用类型保护。比如取这个函数:

```
export function myFunc(a: TypeA[]): void {
  // ...
} 
```

并将其重构为:

```
export function myFunc(a: TypeA[] | TypeB[]): void {
  if (a.every(e => e instanceof TypeA)) {
    // ...
  } else {
    // ...
  }
} 
```

这让我想写一个类型保护的通用版本。那么在一个数组中使用它就像:
`a instanceof Array && a.every(typeGuard<T>)`一样简单。
但是这个`typeGuard<T>`是什么？在上面的例子中，我已经为一些`TypeA`编写了类型保护，所以一个泛型类型保护可以简单地包装对`instanceof`的调用。稍后我们将看到一个更简单的实现。目前，我们有:

```
export function typeGuard<T>(o: any): o is T {
  return o instanceof T;
} 
```

然而，这给了我们一个错误:`'T' only refers to a type, but is being used as a value here.`
这里的问题是类型`T`在运行时并不总是可用的，因为它可能是一个接口——底层 JavaScript 无法访问的构造。这意味着编写泛型类型保护来区分接口是行不通的——尽管可以为特定的接口编写非泛型类型保护。然而，这对于类是有效的:

```
class myClass {}

function classTypeGuard(object: any): boolean {
  return object instanceof myClass;
} 
```

即使我们不想在`T`上变得通用，我们也会得到同样的错误——上面的代码`e instanceof TypeA`给出了同样的关于`TypeA`只引用一个类型的错误。

那么，我们如何传递我们想要检查的函数类型`object`是它的一个实例呢？对于上面的`myClass`类，我们想将`myClass`本身传递给函数，比如:

```
function typeGuard(o, className) {
  return o instanceof className;
}
const myClassObject = new myClass();
typeGuard(myClassObject, myClass); // returns true 
```

## 引入构造函数类型签名

上面的方法是可行的，但是我们没有对`className`变量指定任何类型限制。像`typeGuard(myClassObject, 5)`这样的代码行不会引发错误，但是会导致运行时`TypeError: Right-hand side of 'instanceof' is not an object`。我们需要对`className`的类型添加一个限制，这样只有位于`instanceof`右侧的对象才能被使用。这个限制源于 JavaScript 中对`instanceof`的定义，其中对象需要是某种类型的构造函数。我们可以这样指定`className`的类型:

```
type Constructor<T> = { new (...args: any[]): T };
function typeGuard<T>(o, className: Constructor<T>): o is T {
  return o instanceof className;
}
const myClassObject = new myClass();
typeGuard(myClassObject, myClass); // returns true
typeGuard(myClassObject, 5); // Argument of type '5' is not assignable to parameter of type 'Constructor<{}>' 
```

让我们解开一些我们在这里看到的:我们声明一个新的类型- `Constructor<T>`是一个具有方法`new`的类型，该方法接受任意数量的参数(包括零)并返回类型`T`的实例。这正是我们需要将`className`与`instanceof`一起使用的限制。

## 扩展类型保护为原始类型工作

到目前为止，我们真正做的只是用另一个函数包装`instanceof`,尽管是用看起来很花哨的类型。我们也希望能够这样做:

```
typeGuard(5, 'number'); // true
typeGuard('abc', 'number'); // false 
```

这里我们需要做的是扩展我们正在使用的`myClass`参数的类型，像这样:`type PrimitiveOrConstructor<T> = Constructor<T> | 'string' | 'number' | 'boolean'`。

让我们试着使用这个新类型:

```
type PrimitiveOrConstructor<T> =
  | Constructor<T>
  | 'string'
  | 'number'
  | 'boolean';

function typeGuard<T>(o, className: PrimitiveOrConstructor<T>): o is T {
  if (typeof className === 'string') {
    return typeof o === className;
  }
  return o instanceof className;
}

class A {
  a: string = 'a';
}

class B extends A {
  b: number = 3;
}

console.log(typeGuard(5, 'number'), 'is true');
console.log(typeGuard(5, 'string'), 'is false');

console.log(typeGuard(new A(), A), 'is true');
console.log(typeGuard(new A(), B), 'is false');

console.log(typeGuard(new B(), A), 'is true');
console.log(typeGuard(new B(), B), 'is true');

console.log(typeGuard(new B(), 'string'), 'is false'); 
```

让我们来看看 typeGuard 的新实现:`className`现在要么是一个`Constructor<T>`，要么是一个字符串，其值被限制为`'string'`、`'number'`或`'boolean'`中的一个。如果它是一个字符串(技术上来说，如果它的类型是`'string' | 'number' | 'boolean'`，那么`typeof className === 'string'`将为真，然后类型保护将基于`typeof`而不是`instanceof`。请注意，`if`检查`className`的类型(在`Constructor<T>`的情况下为‘function ’,在其余情况下为‘string ’),类型保护本身将我们要保护的对象的类型与`className`的实际*值*进行比较。

尽管如此，还是有一些问题。在我们检查一个对象是否有原始类型的情况下，`typeGuard`的返回类型是错误的。注意`typeGuard`的返回类型是`o is T`。这个`T`来自`Constructor<T>`，如果那是`className`的类型，但是如果不是，那么`T`被解析为`{}`，这意味着对于原始类型，我们的类型保护是错误的:

```
function typeDependent(o: any) {
  if (typeGuard(o, 'number')) {
    console.log(o + 5); // Error: Operator '+' cannot be applied to types '{}' and '5'
  }
} 
```

我们可以通过让编译器手动知道`T`是什么来纠正这个问题，就像这样:

```
function typeDependent(o: any) {
  if (typeGuard<number>(o, 'number')) {
    console.log(o + 5); // o is number, no error
  }
} 
```

但是我们希望从`className`的值中推断出`typeGuard`的返回类型。我们需要用`PrimitiveOrConstructor<T>`型来防守`T | string | number | boolean`。首先，只有当我们保护的类型不是基本类型时，才应该推断 T 类型。我们将制作一个新的`PrimitiveOrConstructor`，它是*而不是*通用的，然后使用那个类型来推断它正在守卫什么类型。

```
type PrimitiveOrConstructor =
  | { new (...args: any[]): any }
  | 'string'
  | 'number'
  | 'boolean'; 
```

在非原语情况下`PrimitiveOrConstructor`创建的对象的类型没有被指定，因为在解析它所保护的类型时可以推断出来:

```
type GuardedType<T extends PrimitiveOrConstructor> = T extends { new(...args: any[]): infer U; } ? U : T; 
```

现在，如果我们想要类型保护的类型是`aClass`，那么`GuardedType<aClass>`解析为`aClass`。否则，如果我们将`T`设置为`'string'`，那么`GuardedType<'string'>`又只是`'string'`，而不是类型`string`。我们仍然需要能够从类似于`'string'`的字符串值映射到适当的类型，为此我们将引入`keyof`和索引类型。首先，我们将使用类型映射创建从字符串到类型的映射:

```
interface typeMap { // can also be a type
  string: string;
  number: number;
  boolean: boolean;
} 
```

现在，我们可以使用`keyof typeMap`将`'string' | 'number' | 'boolean'`引入到我们的`PrimitiveOrConstructor`中，并索引到`typeMap`中，以获得原始情况下`GuardedType`的适当类型:

```
type PrimitiveOrConstructor =
  | { new (...args: any[]): any }
  | keyof typeMap;

type GuardedType<T extends PrimitiveOrConstructor> = T extends { new(...args: any[]): infer U; } ? U : T extends keyof typeMap ? typeMap[T] : never; 
```

这里需要注意一些事情:

*   `keyof`是一个关键字，它接受一个类型并返回该类型的属性名称的并集。在我们的例子中，`keyof typeMap`正是我们所需要的:`'string' | 'number' | 'boolean'`。这就是为什么`typeMap`的属性名称与它们的类型相同(即字符串属性具有类型`string`，对于`number`和`boolean`也是如此)。
*   `GuardedType<T>`现在使用嵌套的三元组`if` s:我们首先检查我们保护的类型是否有构造函数(`T`是我们给定的提供构造函数的类型，`U`是由那个构造函数实际创建的类型——它们可能是相同的)，然后我们检查`T`是否是原始类型之一，在这种情况下，我们用它来索引我们的`typeMap`，并从`'string'`转到`string`。
*   如果这两个条件都不满足，那么在最后一个分支中使用类型`never`,因为我们永远不会到达它。
*   完全避免第二个`if`会更简单，这样做:

```
 type GuardedType<T extends PrimitiveOrConstructor> = T extends { new(...args: any[]): infer U; } ? U : typeMap[T]; 
```

但是我们得到了这个错误:`Type 'T' cannot be used to index type 'typeMap'.`在`T`不是构造函数类型的情况下，编译器仍然没有将`T`缩小到`keyof typeMap`，因此告诉我们不能安全地使用`T`作为`typeMap`的索引。我们稍后会再次看到这个问题，我觉得这是一个值得一提的开放性问题。我将在附录中详述它。

既然我们已经为给定的`T extends PrimitiveOrConstructor`正确地定义了`GuardedType`，我们可以回到我们的`typeGuard` :
的实现

```
function typeGuard<T extends PrimitiveOrConstructor>(o, className: T):
  o is GuardedType<T> {
    if (typeof className === 'string') {
    return typeof o === className;
  }
  return o instanceof className;
} 
```

我们的`className`参数现在是类型`T extends PrimitiveOrConstructor`，所以`GuardedType<T>`解析成我们想要保护的实际类型——一个类或者一个原始类型。然而，我们*仍然*没有完成，因为我们在最后一行得到了一个错误:

```
return o instanceof className; // The right-hand side of an 'instanceof' expression must be of type 'any' or of a type assignable to the 'Function' interface type. 
```

这里的问题类似于定义`GuardedType`时发生的事情。在这里，`className`的类型在整个函数体中都是`T extends PrimitiveOrConstructor`，尽管我们希望它在`if`子句内缩小到`'string' | 'number' | 'boolean'`，并在其后缩小到`new (...args: any[]) => any`。相反，我们要做的是将`className`赋给一个类型为`PrimitiveOrConstructor`的局部变量，并使用*那个*变量，因为它的类型*将被编译器
缩小*

```
function typeGuard<T extends PrimitiveOrConstructor>(o, className: T):
  o is GuardedType<T> {
    // to allow for type narrowing, and therefore type guarding:
    const localPrimitiveOrConstructor: PrimitiveOrConstructor = className;
    if (typeof localPrimitiveOrConstructor === 'string') {
    return typeof o === localPrimitiveOrConstructor;
  }
  return o instanceof localPrimitiveOrConstructor;
} 
```

## 把所有的东西放在一起

咻，那看起来很难通过。让我们把这些放在一起，这样我们就能看清更大的画面:

```
interface typeMap { // for mapping from strings to types
  string: string;
  number: number;
  boolean: boolean;
}

type PrimitiveOrConstructor = // 'string' | 'number' | 'boolean' | constructor
  | { new (...args: any[]): any }
  | keyof typeMap;

// infer the guarded type from a specific case of PrimitiveOrConstructor
type GuardedType<T extends PrimitiveOrConstructor> = T extends { new(...args: any[]): infer U; } ? U : T extends keyof typeMap ? typeMap[T] : never;

// finally, guard ALL the types!
function typeGuard<T extends PrimitiveOrConstructor>(o, className: T):
  o is GuardedType<T> {
    const localPrimitiveOrConstructor: PrimitiveOrConstructor = className;
    if (typeof localPrimitiveOrConstructor === 'string') {
    return typeof o === localPrimitiveOrConstructor;
  }
  return o instanceof localPrimitiveOrConstructor;
} 
```

为了进行测试，让我们使用与之前相同的例子，只是现在类型保护将实际工作，并根据需要为我们提供`string`、`number`、`A`或`B`:

```
class A {
  a: string = 'a';
}

class B extends A {
  b: number = 5;
}

console.log(typeGuard(5, 'number'), 'true'); // typeGuard<"number">(o: any, className: "number"): o is number
console.log(typeGuard(5, 'string'), 'false'); // typeGuard<"string">(o: any, className: "string"): o is string

console.log(typeGuard(new A(), A), 'true'); // typeGuard<typeof A>(o: any, className: typeof A): o is A
console.log(typeGuard(new B(), A), 'true');

console.log(typeGuard(new A(), B), 'false'); // typeGuard<typeof B>(o: any, className: typeof B): o is B
console.log(typeGuard(new B(), B), 'true');

console.log(typeGuard(new B(), 'string'), 'false'); 
```

## 总结

经历了以上所有，我意识到用`instanceof`测试特殊情况，用用户定义的类型保护测试接口，用`typeof`测试原语几乎总是更简单。

我确实从自己尝试解决这个问题中学到了很多，尤其是从用户`jcalz`的 StackOverflow 回答中。这篇文章主要是通过他们的回答，并解释它的不同部分。浏览这个实现的步骤包括理解 typescript 的类型系统、泛型、类型保护、有用的关键字如`keyof`和`infer`、联合类型和索引类型。

## 来源

[StackOverflow 关于尝试在泛型类型上调用 instanceof 的回答](https://stackoverflow.com/a/46703380/6338059)

[引用 typeScript 中类型的构造函数(一般)](http://blakeembrey.com/articles/2015/04/typescript-constructor-of-type/)

## 补遗

当我们在`GuardedType`和`typeGuard`中使用`T extends PrimitiveOrConstructor`时，我们看到关于`T`类型的条件(例如扩展构造函数还是扩展`keyof typeMap`)并没有帮助编译器缩小`T`的类型，即使我们将`PrimitiveOrConstructor`定义为构造函数类型或者有效的属性名`typeMap`。

在`GuardedType`的定义中，检查构造函数类型情况的`else`分支没有让我们索引到`typeMap`，尽管那是`T`唯一的其他选项。在`typeGuard`函数的实现中，我们试图以相反的顺序做同样的事情——我们检查了覆盖`T extends keyof typeMap`情况的`typeof className === 'string'`，但是在这个子句之外`T`没有被缩小到一个构造函数类型。

为了定义`GuardedType`，我们必须显式地编写第二个三元组`if`，让编译器知道`T extends keyof typeMap`，这样我们就可以将类型解析为`typeMap[T]`。为了实现`typeGuard`，我们需要将`className`(类型为`T extends PrimitiveOrConstructor`)赋给类型为`PrimitiveOrConstructor`的局部变量。该变量的类型根据需要缩小到`if`子句内的`'string' | 'number' | 'boolean'`，以及其后的`new (...args: any[]) => any`。

这两种情况下的问题是`T`是一个泛型类型，它扩展了联合类型`PrimitiveOrConstructor`。截至目前(2019-04-07)这是[一个未解决的问题](https://github.com/Microsoft/TypeScript/issues/13995)。幸运的是，在`jcalz`的 StackOverflow 回答中也提到了这一点。