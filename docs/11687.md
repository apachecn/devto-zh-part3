# 关于 TypeScript 的注释:入门基础

> 原文：<https://dev.to/busypeoples/notes-on-typescript-fundamentals-for-getting-started-3cjo>

#### 简介

想开始使用 TypeScript 的 JavaScript 开发人员的教程。
对所有基本功能的彻底演练。
我们将浏览基本特性，以便更好地理解基本原理。

#### 设置

当前版本:TypeScript 3.2

*   请务必参考官方文档以获得更深入的理解。[https://www.typescriptlang.org/](https://www.typescriptlang.org/)
*   安装和设置:[https://www.typescriptlang.org/samples/index.html](https://www.typescriptlang.org/samples/index.html)
*   在线试玩回复:[https://www.typescriptlang.org/play/index.html](https://www.typescriptlang.org/play/index.html)

本教程还提供了一个 JavaScript 文件。你可以在这里找到文件[。](https://gist.github.com/busypeoples/270f29ec6cdd45780889c53e7b52884a)

你可以通过 TypeScript playground 在线浏览教程:
将[这个文件](https://gist.github.com/busypeoples/270f29ec6cdd45780889c53e7b52884a)复制到在线编辑器中。

#### 基础知识

让我们从几个非常基本的例子开始。

```
const a: string = "foo";
const b = 1;
const c = false;
const d = [1, 2, 3];
const e = ["a", "b", "c"];
const f = { id: 1 };
const g = null;
const h = undefined; 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript 提供了许多类型。为了得到一个快速的概述，让我们明确地定义上述常量的类型。

```
`const a : string = "foo"` (ok)
---------------------------------------------------------------------
`const a : number = "foo"` (error)
We will get the following error:
Type '"foo"' is not assignable to type 'number'.
---------------------------------------------------------------------
So assign the type for learning purposes. In reality you would rely on type inference. 
```

Enter fullscreen mode Exit fullscreen mode

```
const aTyped: string = 'foo'
const bTyped: number = 1
const cTyped: boolean = false 
```

Enter fullscreen mode Exit fullscreen mode

前三个比较清楚。但是我们如何输入`d`？

```
const dTyped: number[] = [1, 2, 3]
// or
const dTyped : Array<number> = [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

让我们继续:

```
const eTyped: Array<string> = ["a", "b", "c"];
const fTyped: Object = { id: 1 };
// or better
const fTyped: { id: number } = { id: 1 };
const gTyped: null = null 
```

Enter fullscreen mode Exit fullscreen mode

未定义呢？
在 TypeScript 中，你可以使用`undefined`类型来声明一个未定义的值。
随着我们的进展，我们将会进入更多的细节，并且更加详细地介绍原语。

```
const hTyped : undefined = undefined; 
```

Enter fullscreen mode Exit fullscreen mode

让我们继续更好地理解 TypeScript 中的基本类型。
例如,`i`和`j`可以被指定为原始类型，但有趣的是也可以被指定为文字类型。那会是什么样子？

```
const i = 2;
const j = "foo";

const iTyped: 2 = 2;
const jTyped: "foo" = "foo"; 
```

Enter fullscreen mode Exit fullscreen mode

现在你可能想知道我们从文字类型中获得了什么价值？我们可以限制我们期望的价值。

```
type ExpectedInput = 1 | 2 | 3

const doSomething = (input: ExpectedInput) => {
  switch (input) {
    case 1:
      return 'Level 1'
    case 2:
      return 'Level 2'
    case 3:
      return 'Level 3'
  }
}

doSomething(0) // error: This type is incompatible with the expected param type of number enum
doSomething(1) // ok 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们一直在处理常量变量。let 或者 var 呢？而常量变量不能被重新赋值，所以 TypeScript 可以改变类型，并确定它永远不会改变。使用 let 或 var 时，情况并非如此。

```
let aVar: string = "foo";

aVar = 'bar'
aVar = 1  // Error! 
```

Enter fullscreen mode Exit fullscreen mode

正如我们在上面例子中所看到的，一旦你将一个类型赋给了一个 let 或 var 变量，任何重新赋值都必须是相同的类型，否则 TypeScript 就会报错。

```
Take a look at the following example:
---------------------------------------------------------------------
const i : 3 = 2
Error: Type '2' is not assignable to Type '3'.
--------------------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

### 任意 Vs .未知

有时你不能告诉确切的类型是什么，或者你正在从一个现有的非类型代码库逐渐转换。这就是`any`和`unknown`有用的地方。重要的是要注意它们实现不同的目的。`any`应该作为最后的手段使用，因为它跳过了类型检查。
相反`unknown`在你不能确定输入类型是什么的时候很有用，顾名思义。查看下面的例子:

```
const double = (input: unknown) => {
  if (typeof input === 'string') {
    return input + ' - ' + input
  }
  if (Array.isArray(input)) {
    return input.concat(input)
  }
  return input
}

const result = double('foo') // ok 
```

Enter fullscreen mode Exit fullscreen mode

我们需要通过检查类型来细化输入，然后返回一个合适的值，否则编译器会报错。
使用`any`，我们完全绕过了类型检查器。我们可以向`length`传递任何值，永远不会收到错误。如前所述，如果可能的话，把`any`作为最后的手段！

```
const length = (input: any) => {
  if (typeof input === "string") {
    return input.length;
  }

  if (Array.isArray(input)) {
    return input.length;
  }

  return 0;
};

length("foo");
length([1, 2, 3, 4]);
length(1); // no Error! 
```

Enter fullscreen mode Exit fullscreen mode

#### 可选值

有时我们希望某个值是可选的。例如看一下下面的函数:

```
const optionalLength = (input?: string | Array<any>) => {
  if (typeof input === "string") {
    return input.length;
  }

  if (Array.isArray(input)) {
    return input.length;
  }

  return false;
};

optionalLength();
optionalLength(undefined);
optionalLength([1, 2, 3, 4]);
optionalLength("foo"); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所见，我们可以用未定义的数组或字符串调用 optionalLength。但是正如你所料，传入一个数字会导致错误。

```
optionalLength(1) // Error! 
```

Enter fullscreen mode Exit fullscreen mode

传入 null 也会导致错误:

```
optionalLength(null); // error! We need to be explicit about null 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，我们需要将类型签名改为期望 null:
`const optionalLength = (input?: string | Array<any> | null) => {};`

#### 功能

现在我们已经介绍了非常基础的内容，是时候学习更高级的内容了。在前一节中我们已经看到了几个函数，但是让我们更详细地看看函数类型。首先，我们想要输入一个函数的输入和输出，所以让我们看看这是如何完成的。

```
let add = (a: number, b: number): number => {
  return a + a;
};

add(2, 2);
add(2, "a"); // Error!
const addResult: number = add(2, 2); 
```

Enter fullscreen mode Exit fullscreen mode

尝试运行以下命令:

```
const addResultError : string = add(1, 2); // Error! 
```

Enter fullscreen mode Exit fullscreen mode

您将看到以下错误:

```
---------------------------------------------------------------------
Type 'number' is not assignable to type 'string'.
--------------------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

如果你想使用传统的函数声明而不是箭头函数，你可以这样写前面的例子:

```
let add = (a: number, b: number): number => {
  return a + a;
};

add(2, 2);
add(2, "a"); // Error!
const addResult: number = add(2, 2); 
```

Enter fullscreen mode Exit fullscreen mode

欲了解更多信息，请查看此处的函数[文档。](https://www.typescriptlang.org/docs/handbook/functions.html)

#### 数组

让我们继续数组。如果您还记得最开始的时候，我们输入了一个简单的数组。数组有两种类型:`Array<Type> or Type[]`。
所以这两个是等价的:

```
const aArray : Array<number> = [1, 2, 3]
const aArrayShortHand : number[] = [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

如果数组中有一个空值呢？答案和我们在可选部分看到的
非常相似。在这种特定的情况下，我们需要明确表示 null 或 undefined。

```
const aOptionalArray: Array<number | null | undefined> = [
  1,
  null,
  2,
  undefined
];
const aOptionalArrayShortHand: (number | null | undefined)[] = [
  1,
  null,
  2,
  undefined
]; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想更具体地定义数组呢？
举下面这个例子:
我们有一个正好由三个条目组成的数组，简而言之就是一个包含一个字符串、一个数字和另一个字符串的元组:`['foo', 1, 'bar']`。

```
const tupleA: [string, number, string] = ["foo", 1, "bar"];
const tupleB: [string, number, number] = ["foo", 1, "bar"]; // Error! 
```

Enter fullscreen mode Exit fullscreen mode

另一个重要的方面是，一旦定义了元组，您仍然可以使用任何现有的数组方法来改变数组。编译器不会抱怨，而 Flow 编译器会抱怨。

```
tupleA.push("foobar");
tupleA.push(null); // Error! 
```

Enter fullscreen mode Exit fullscreen mode

因此，一旦定义了元组，就可以推送元组中存在的任何类型。例如，将空值推入一个需要字符串或数字的元组会导致错误:

```
---------------------------------------------------------------------------------
Argument of Type 'null' is not assignable to parameter of type 'string | number'.
--------------------------------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

接下来让我们看看传统阵列:

```
const bArray: Array<number> = [1, 2, 3];
bArray.push(4);
bArray.push("foo"); // Error! 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到与前面的元组示例类似的结果。只要类型匹配，就可以推入现有数组。例如，将字符串“foo”推入一个数字数组会导致以下错误:

```
---------------------------------------------------------------------------------
Argument of Type '"foo"' is not assignable to parameter of type 'number'.
--------------------------------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

#### 物体

接下来，我们将了解 TypeScript 如何处理对象。

```
const aObject: Object = { id: 1, name: "foo" };
const bObject: { id: number } = { id: 1, name: "foo" }; // !Error 
```

Enter fullscreen mode Exit fullscreen mode

`bObject`将导致错误，因为`name`未定义。我们需要明确类型定义。

```
---------------------------------------------------------------------------------
Type '{id: number; name: string;}' is not assignable to type '{id: number;}'.
--------------------------------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

```
const cObject: { id: number; name: string } = { id: 1, name: "foo" };
const dObject : {id: number, name: string, points: number} = {id: 1, name: 'foo'} // Error! 
```

Enter fullscreen mode Exit fullscreen mode

`dObject`将导致一个错误，因为点没有被定义。我们想让分数可选。我们已经看到了如何使一个值成为可选的，所以让我们看看如何对一个对象属性实现同样的效果。

```
const dRefinedObject : {id: number, name: string, points?: number} = {id: 1, name: 'foo'} 
```

Enter fullscreen mode Exit fullscreen mode

通过声明`points?: number`，我们说点可能没有被定义。为了提高可读性，您可能会求助于为对象声明定义一个类型别名。如果您还计划重用类型定义，这尤其有用。

```
type E = { id: number; name: string; points?: number };
const eObject: E = { id: 1, name: "foo" }; 
```

Enter fullscreen mode Exit fullscreen mode

在处理对象时，另一个需要注意的重要事情是，我们不能给一个已定义的对象添加不存在的属性。看看下面的代码片段:

```
const fObject = {
  id: 1
}

fObject.name = 'foo' // Error! 
```

Enter fullscreen mode Exit fullscreen mode

试图将`name`赋值给我们的`fObject`会导致以下错误:

```
---------------------------------------------------------------------------------
Property 'name' does not exist on type {id: number;}.
--------------------------------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

所以以上是不行的。根据定义，我们可以向具有已定义属性的对象添加新属性。我们需要更加明确的类型，以便能够使对象可扩展。我们可以定义一个类型，期望一个字符串类型的键和字符串属性类型，例如

```
const gObject: { [key: string]: string } = {};
gObject.name = "foo"; 
```

Enter fullscreen mode Exit fullscreen mode

处理对象的另一个重要方面是我们必须精确定义类型。请看下一个例子:

```
type F = {id: number, name: string}
const fObject : F = {id: 1, name: 'foo', points: 100} // Error! 
```

Enter fullscreen mode Exit fullscreen mode

但是如果通缉不能准确呢？我们可以解决必须精确的问题:

```
type G = {id: number, name: string, [key: string]: string | number};
const gObject : G = {id: 1, name: 'foo', points: 100} // No Error! 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 中一种常见的方法是使用对象作为地图:

```
const aMap: { [key: number]: string } = {};
aMap[1] = "foo";
aMap["a"] = "foo"; // Error!
aMap[1] = 1; // Error!

const otherMap: { [key: string]: number } = {};
otherMap["foo"] = 1;
otherMap[1] = 2; // No Error!
otherMap["bar"] = "foo"; // Error! 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以将属性声明与动态键/值对混合使用，正如前面的例子所示:

```
const mixedMap: {
  id: number;
  [key: string]: number;
} = {
  id: 1
};

mixedMap["foo"] = 1;
mixedMap[1] = 2; // No Error!
mixedMap["bar"] = "foo"; // Error! 
```

Enter fullscreen mode Exit fullscreen mode

#### 类

在本节中，我们将尝试理解在 TypeScript 中类是如何类型化的。键入类实例时，可以引用类本身。

```
class Foo {
  state = { val: 0 };
  update(val: number) {
    this.state = { val };
  }
  getVal() {
    return this.state.val;
  }
}

const foobar: Foo = new Foo(); 
```

Enter fullscreen mode Exit fullscreen mode

类方法和属性可以像你期望的那样被类型化。让我们更新前面的例子。

```
class Foo {
  state: { val: number } = { val: 0 };
  update(val: number): void {
    this.state = { val };
  }
  getVal(): number {
    return this.state.val;
  }
}

const foobar: Foo = new Foo();

foobar.update(3);
foobar.update("foo"); // Error!

const fooResult: number = foobar.getVal();
const fooResultError: string = foobar.getVal(); // Error! 
```

Enter fullscreen mode Exit fullscreen mode

#### 界面

为了使事情圆满，让我们也来看看接口。如果我们有一个类栏，也有一个状态属性和一个更新功能，会怎么样？

```
interface Updateable<T> {
  state: { val: T };
  update(a: T): void;
}

class InterfaceExample implements Updateable<boolean> {
  state = { val: false };
  constructor(val: boolean) {
    this.state = { val };
  }
  update(val: boolean) {
    this.state = { val };
  }
  getValue() {
    return this.state.val;
  }
}

const exampleInstance = new InterfaceExample(true);
const exampleInstanceResultOk: boolean = exampleInstance.getValue();
const exampleInstanceResultError: number = exampleInstance.getValue(); // Error! 
```

Enter fullscreen mode Exit fullscreen mode

有关更多信息，请查看:

*   [https://www.typescriptlang.org/docs/handbook/classes.html](https://www.typescriptlang.org/docs/handbook/classes.html)
*   [https://www . typescriptlang . org/docs/handbook/type-inference . html](https://www.typescriptlang.org/docs/handbook/type-inference.html)

#### 仿制药

现在我们进入了更高级的领域。到目前为止，我们应该已经掌握了所有必要的基础知识。让我们继续前面的例子，添加泛型。例如，我们的示例类也可能接受字符串而不是数字。在这种情况下，我们希望抽象类型定义。

```
class Example<T> {
  state: { val: T };
  constructor(input: T) {
    this.state = { val: input };
  }
  update(val: T): void {
    this.state = { val };
  }
  getVal(): T {
    return this.state.val;
  }
}

const exampleGenericNumber: Example<number> = new Example(1);
exampleGenericNumber.update(2);
const exampleGenericResult: number = exampleGenericNumber.getVal();

const exampleGenericString = new Example("one");
const exampleGenericResultOther: string = exampleGenericString.getVal(); 
```

Enter fullscreen mode Exit fullscreen mode

如果你取消了上面例子的注释，你会发现一切正常。有趣的是，你甚至不必为`const exampleGenericString = new Example('foo')`明确定义一个类型。
TypeScript 会知道我们的返回值是一个字符串，如下面一行所示。我们可以用泛型做更多的事情，比如定义类型别名或函数。让我们看一些例子来更好地了解可能性。

```
type ExampleGeneric<T, U> = {
  one: T;
  two: U;
};

const GenericAlias: ExampleGeneric<number, boolean> = {
  one: 1,
  two: false
};

const GenericAliasError: ExampleGeneric<number, boolean> = {
  one: 1,
  two: "foo"
}; //Error! 
```

Enter fullscreen mode Exit fullscreen mode

#### 具有函数的泛型

```
const identity = <T>(a: T): T => {
   return a;
};

const doubleIfPossibleResultOne: number = <number>identity(2);
// Optional: We don't need to be specific about the type, TypeScript can infer the argument type.
const doubleIfPossibleResultOne: number = identity(2);
const doubleIfPossibleResultTwo: string = identity("foo");
const doubleIfPossibleResultError: string = identity(true); // Error! 
```

Enter fullscreen mode Exit fullscreen mode

使用泛型可以做更多的事情。如果您有兴趣了解关于该主题的更多信息，请查阅官方文档。

更多信息请查看:[https://www.typescriptlang.org/docs/handbook/generics.html](https://www.typescriptlang.org/docs/handbook/generics.html)

#### 只读

有时我们希望确保对象属性是只读的。看看下面的例子:

```
type AB = { a: number; b: string };

const readOnlyNone = (o: AB) => {
  o.a = 100; // No Error!
  return o;
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将我们的`a`属性重写为`readonly a`，并确保`a`现在是只读的。

```
type ReadOnlyA = { readonly a: number; b: string };

const readOnlyA = (o: ReadOnlyA) => {
  o.a = 100; // Error!
  o.b = "test"; // No Error!
  return o;
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们想让我们的对象只读，我们可以使用内置的`Readonly`类型。

```
type ReadOnlyAB = Readonly<{ a: number, b: string }>;

const readOnlyAB = (o: ReadOnlyAB) => {
  o.a = 100; // Error!
  o.b = 'test' // Error!
  return o
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 交集

有时我们需要定义一个类型，它是其他类型的集合。我们可以用交集来组合类型，看看下面的例子。

```
type InterA = { id: number };
type InterB = { name: string };
type InterC = { email: string };

type User = InterA & InterB & InterC;

const addUser = (users: Array<User>, user: User): Array<User> => [
  ...users,
  user
];
addUser([], { id: 1, name: "user a" }); // Error!
addUser([], { id: 1, name: "user b", email: "some.email@some.email" }); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在应该对 TypeScript 基础有了很好的理解。

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)