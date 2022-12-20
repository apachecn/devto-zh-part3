# 关于类型脚本的注释:React 和泛型

> 原文：<https://dev.to/busypeoples/-notes-on-typescript-react-and-generics-35c9>

### 简介

这些注释应该有助于更好地理解`TypeScript`，并且在需要查找在特定情况下如何利用 TypeScript 时可能会有帮助。所有示例都基于 TypeScript 3.2。

### 仿制药

如果您一直在阅读“关于 TypeScript 的笔记”系列，那么到目前为止，您已经看到了泛型的广泛使用。虽然我们一直在使用泛型，但我们实际上并没有谈到泛型以及它们为什么有用。在本系列的这一部分中，我们将首先尝试更好地理解泛型主题，然后看看在使用 React 和 TypeScript 时如何利用泛型。

编写软件时，一个方面是我们希望能够重用一些功能，而不必为每种可能的输入类型编写特定的功能。让我们以下面的例子为起点:

```
function isDefinedNumber(a: number) : boolean {
  return a !== null || a !== undefined;
}

function isDefinedString(a: string) : boolean {
  return a!== null || a !== undefined;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们不会为`string`或`number`输入编写显式函数，而是编写一个具有以下特征的函数:

```
function isDefined<Type>(a: Type) : boolean {
  return a!== null || a !== undefined;
} 
```

Enter fullscreen mode Exit fullscreen mode

`isDefined`期望一个泛型`Type`的输入。TypeScript 将尝试推断参数并分配正确的类型。让我们继续另一个例子，我们想推断返回类型:

```
function of<Type>(a: Type) : Type[] {
  return [a];
}

const toNumbers = of(1); // const toNumbers: number[]
const toStrings = of("Test Of"); // const toString: string[] 
```

Enter fullscreen mode Exit fullscreen mode

在`of`的例子中，我们可以看到我们甚至不需要定义类型，因为 TypeScript 可以推断参数类型。这并不适用于所有情况，有时我们必须明确类型。我们也可以这样定义上面的函数:

```
const toNumbers = of<number>(1); // const toNumbers: number[]
const toStrings = of<string>("Test Of"); // const toString: string[] 
```

Enter fullscreen mode Exit fullscreen mode

从技术上讲，我们可以使用`any` :

```
function of(a: any) : any {
  if (a.length !== undefined) {
    return a
  }
  return a;
} 
```

Enter fullscreen mode Exit fullscreen mode

但是使用`any`和泛型有很大的区别。如果你仔细看看上面的例子，我们对输入参数一无所知。用`undefined`或`null`值调用`of`会导致错误。泛型可以推断出确切的类型，并强制在函数体内相应地处理输入。同一个使用泛型的例子:

```
function of<Type>(a: Type) : Type[] {
  if (a.length !== undefined) { // error: Property 'length' does not exist on 'Type'
    return a
  }
  return [a];
} 
```

Enter fullscreen mode Exit fullscreen mode

在处理泛型时，我们必须更加明确，这个例子可以重写为:

```
function of<Type>(a: Type | Type[]) : Type[] {
  if (Array.isArray(a)) {
    return a
  }
  return [a];
}

const a = of(1); // const a: number[]
const b = of([1]); // const b: number[] 
```

Enter fullscreen mode Exit fullscreen mode

使用泛型使我们能够重用功能，因为`a`是类型`Type`或类型`Type`的数组。当传入`1`作为参数时，`Type`绑定到`number`，传入`[1]`时也是如此，`Type`绑定到`number`。

虽然我们已经看到了使用泛型的函数，但我们也可以将泛型用于类，这在 React 中编写类组件时可能会很有趣。

```
class GenericClass<Type> {
  of = (a: Type | Type[]): Type[] => {
    if (Array.isArray(a)) {
      return a;
    }
    return [a];
  };
}

const genericClass = new GenericClass<number>();
const a = genericClass.of(1); // const a: number[]
const b = genericClass.of("1"); // error!
const c = genericClass.of([1]); // const c: number[] 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们看到的例子应该有助于我们理解基础知识，我们将在 React 组件中使用泛型时建立这些知识。

### 反应和泛型

使用 React 时，我们可能需要一个函数组件来推断参数类型。我们可能正在构建一个需要数字或字符串或者数字或字符串类型的数组的组件。

```
type RowProps<Type> = {
  input: Type | Type[];
};

function Rows<Type>({input}: RowProps<Type>) {
  if (Array.isArray(input)) {
    return <div>{input.map((i, idx) => <div key={idx}>{i}</div>)}</div>
  }
  return <div>{input}</div> }

// usage

<Rows input={[1]} /> <Rows input={1} /> <Rows input={true} /> // Also works! 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但它现在也适用于任何值。我们可以传入`true`，TypeScript 不会抱怨。我们需要通过确保`Type`扩展`number`或`string`来限制`Type`。

```
function Rows<Type extends number | string>({input}: RowProps<Type>) {
  if (Array.isArray(input)) {
    return <div>{input.map((i, idx) => <div key={idx}>{i}</div>)}</div>
  }
  return <div>{input}</div> }

<Rows input={[1]} /> <Rows input={1} /> <Rows input="1" />
<Rows input={["1"]} /> <Rows input={true} /> //Error! 
```

Enter fullscreen mode Exit fullscreen mode

我们可以确保现在只能提供预期的类型。有趣的是，我们可以使我们的属性类型定义通用化，如上面的例子所示:

```
type RowProps<Type> = {
  input: Type | Type[];
}; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将构建一个更高级的示例，看看为什么泛型可以帮助我们构建可重用的 React 组件。我们将构建一个期望两个不同输入的组件。基于这些输入，我们将计算第三个值，并根据原始输入和新计算的值传递给一个提供的渲染属性。

```
type RenderPropType<InputType, OtherInputType> = { c: number } & InputType &
  OtherInputType;

type RowComponentPropTypes<InputType, OtherInputType> = {
  input: InputType;
  otherInput: OtherInputType;
  render: (props: RenderPropType<InputType, OtherInputType>) => JSX.Element;
}; 
```

Enter fullscreen mode Exit fullscreen mode

第一步是定义`RowComponentPropTypes`，在这里我们让 TypeScript 推断所提供的参数，并基于绑定类型通过使用`RenderPropType`来定义`render`函数。`RenderPropType`是我们将要计算的新型`{c: number}`与`InputType`和`OtherInputType`的交集。到目前为止，我们一直在大量使用泛型。

我们可能不知道所提供的输入的确切形状，所以我们的下一步是在组件级别上限制所提供的类型。

```
class RowComponent<
  InputType extends { a: number },
  OtherInputType extends { b: number }
> extends React.Component<RowComponentPropTypes<InputType, OtherInputType>> {
  // implementation...
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`InputType extends { a: number }`，我们可以确保我们的输入有一个`number`类型的`a`属性，对于`OtherInputType`也是如此。现在我们可以实现`RowComponent`，确保我们可以向`render`函数提供`a, b, c`属性。

最后，这是我们完整的示例实现:

```
class RowComponent<
  InputType extends { a: number },
  OtherInputType extends { b: number }
> extends React.Component<RowComponentPropTypes<InputType, OtherInputType>> {
  convert = (input: InputType, output: OtherInputType) => {
    return { c: input.a + output.b, ...input, ...output };
  };
  render() {
    return this.props.render(
      this.convert(this.props.input, this.props.otherInput)
    );
  }
}

<RowComponent
  input={{ a: 1 }}
  otherInput={{ b: 2 }}
  render={({ a, b, c }) => (
    <div>
      {a} {b} {c}
    </div>
  )}
/> 
```

Enter fullscreen mode Exit fullscreen mode

我们现在应该对泛型有一个基本的了解，以及在使用 React 和 TypeScript 时如何利用它们。

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)