# 关于类型脚本的注释:幻象类型

> 原文：<https://dev.to/busypeoples/notes-on-typescript-phantom-types-kg9>

### 简介

这些注释应该有助于更好地理解`TypeScript`，并且在需要查找在特定情况下如何利用 TypeScript 时可能会有帮助。所有示例都基于 TypeScript 3.2。

### 幻影类型

在下面的“关于打字稿的注释”中，我们将讨论`Phantom Types`。
为了更好地理解`Phantom Types`，我们将一路构建示例，展示在哪里使用它们是有用的。

*"幻象类型是一种参数化类型，其参数并不都出现在其定义的右侧..."*
[哈斯克尔维基，PhantomType](https://wiki.haskell.org/Phantom_type)

看一下 Haskell wiki 中的上述定义，我们知道幻像类型是参数化类型，不是所有的参数都出现在右边。让我们试试看能否在 TypeScript 中实现一个类似的例子。

```
type FormData<A> = string; 
```

Enter fullscreen mode Exit fullscreen mode

`FormData`是一个虚拟类型，因为`A`参数只出现在左侧。

接下来，我们想让一个库用户创建一个`FormData`类型。我们还希望在库的某些部分限制类型。例如，我们希望能够区分*已验证的*和*未验证的*表单数据。所以让我们添加两个类型定义`Validated`和`Unvalidated`。

```
type Unvalidated = {_type: "Unvalidated"};
type Validated = {_type: "Validated"}; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们实现一个`FormData`类型，使得表单库的消费者无法覆盖`value`类型定义。在这种特殊情况下，我们将用一个`never`类型来定义`value`。

```
type FormData<T, D = never> = {value: never} & T; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们想要公开一个接收字符串并返回未验证的`FormData`类型的函数。

```
type makeFormData = (a: string) => FormData<Unvalidated>; 
```

Enter fullscreen mode Exit fullscreen mode

也许我们想添加一个`upperCase`函数来做这件事，取一个未验证的`FormData`并返回一个未验证的`FormData`。

```
type upperCase = (a: FormData<Unvalidated>) => FormData<Unvalidated>; 
```

Enter fullscreen mode Exit fullscreen mode

让我们再添加一个`validate`定义，它或者返回经过验证的输入，或者返回 null。

```
type Validate = (a: FormData<Unvalidated>) => FormData<Validated> | null; 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们添加一个函数来处理经过验证的数据:

```
type Process = (a: FormData<Validated>) => FormData<Validated>; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经定义了表单帮助函数，让我们看看如何实现这些定义，以确保实际的表单值总是对开发人员隐藏。

```
export const makeFormData: MakeFormData = value => {
  return { value } as FormData<Unvalidated>;
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们回忆一下我们的`makeFormData`函数接受一个字符串并返回一个`FormData<Unvalidated>`，需要注意的是，这应该是创建这种类型的唯一方法。开发商土地被禁止定义`value`，因为它被定义为`never`类型。
一旦创建了这种类型，开发人员就可以使用返回值来验证或大写该值。

让我们看看如何实现`upperCase`和`validate`函数。

```
export const upperCase: UpperCase = data => {
  const internalData = data as InternalUnvalidated;
  return { value: internalData.value.toUpperCase() } as FormData<Unvalidated>;
};

export const validate: Validate = data => {
  const internalData = data as InternalUnvalidated;
  if (internalData.value.length > 3) {
    return { value: internalData.value } as FormData<Validated>;
  }
  return null;
}; 
```

Enter fullscreen mode Exit fullscreen mode

看上面两个函数，有一个重要的方面我们可以观察到。我们需要对提供的输入进行内部强制转换。但是什么是`InternalUnvalidated`？

```
type InternalUnvalidated = Unvalidated & {
  value: string;
};

type InternalValidated = Validated & {
  value: string;
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里所做的，是定义我们数据的内部表示，对使用库的开发人员来说是隐藏的。在这种情况下，我们声明`value`是一个*字符串*。
`process`可以用与上述函数相同的方式编写，只是我们将转换为`InternalValidated`类型，因为我们期待的是`FormData<Validated>`类型。

```
export const process : Process = (data: FormData<Validated>) => {
  const internalData = data as InternalValidated;
  // do some processing...
  return data; // cast to FormData<Validated>
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经实现了幻像类型，并且可以验证我们的库如预期的那样工作:

```
import { makeFormData, validate, upperCase, process } from './phantomTypes'

const initialData = makeFormData("test");
const validatedData = validate(initialData);

// validate("hello") // Type '"hello"' is not assignable to type '{value: never}'
// validate({value: "hello"}) // Type 'string' is not assignable to type 'never'

if (validatedData !== null) {
  // validate(validatedData); // Error! Type '"Validated"' is not assignable to Type '"Unvalidated"'
  upperCase(initialData);
  // upperCase(validatedData) // Error! Type '"Validated"' is not assignable to Type '"Unvalidated"'
  process(validatedData);
  // process(initialData); // Error! Type '"Unvalidated"' is not assignable to Type '"Validated"'
} 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以创建自己的`PhantomType`类型和抽象，手动处理`_type` :

```
type PhantomType<Type, Data> = {_type: Type} & Data;

// use this type helper to create an UnvalidatedData type
type UnvalidatedData = PhantomType<"Unvalidated", {value: string}> 
```

Enter fullscreen mode Exit fullscreen mode

至此，我们现在应该对如何在 TypeScript 中实现*幻影类型*有了一个基本的了解。查看完整示例[点击](https://www.typescriptlang.org/play/index.html#src=type%20FormData_%3CT%2C%20D%20%3D%20never%3E%20%3D%20%7B%20value%3A%20never%20%7D%20%26%20T%3B%0D%0A%0D%0Atype%20Unvalidated%20%3D%20%7B_type%3A%20%22Unvalidated%22%7D%3B%0D%0Atype%20Validated%20%3D%20%7B%20_type%3A%20%22Validated%22%20%7D%3B%0D%0A%0D%0Atype%20MakeFormData%20%3D%20(a%3A%20string)%20%3D%3E%20FormData_%3CUnvalidated%3E%3B%0D%0Atype%20UpperCase%20%3D%20(a%3A%20FormData_%3CUnvalidated%3E)%20%3D%3E%20FormData_%3CUnvalidated%3E%3B%0D%0Atype%20Validate%20%3D%20(a%3A%20FormData_%3CUnvalidated%3E)%20%3D%3E%20FormData_%3CValidated%3E%20%7C%C2%A0null%3B%0D%0Atype%20Process%20%3D%20(a%3A%20FormData_%3CValidated%3E)%20%3D%3E%20FormData_%3CValidated%3E%3B%0D%0A%0D%0Atype%20InternalUnvalidated%20%3D%20Unvalidated%20%26%20%7B%0D%0A%20%20value%3A%20string%3B%0D%0A%7D%3B%0D%0A%0D%0Atype%20InternalValidated%20%3D%20Validated%20%26%20%7B%0D%0A%20%20value%3A%20string%3B%0D%0A%7D%3B%0D%0A%0D%0Aconst%20makeFormData%3A%20MakeFormData%20%3D%20value%20%3D%3E%20%7B%0D%0A%20%20return%20%7B%20value%20%7D%20as%20FormData_%3CUnvalidated%3E%3B%0D%0A%7D%3B%0D%0A%0D%0Aconst%20upperCase%3A%20UpperCase%20%3D%20data%20%3D%3E%20%7B%0D%0A%20%20const%20internalData%20%3D%20data%20as%20InternalUnvalidated%3B%0D%0A%20%20return%20%7B%20value%3A%20internalData.value.toUpperCase()%20%7D%20as%20FormData_%3CUnvalidated%3E%3B%0D%0A%7D%3B%0D%0A%0D%0Aconst%20validate%3A%20Validate%20%3D%20data%20%3D%3E%20%7B%0D%0A%20%20const%20internalData%20%3D%20data%20as%20InternalUnvalidated%3B%0D%0A%20%20if%20(internalData.value.length%20%3E%203)%20%7B%0D%0A%20%20%20%20return%20%7B%20value%3A%20internalData.value%20%7D%20as%20FormData_%3CValidated%3E%3B%0D%0A%20%20%7D%0D%0A%20%20return%20null%3B%0D%0A%7D%3B%0D%0A%0D%0Aconst%20process%20%3A%20Process%20%3D%20(data%3A%20FormData_%3CValidated%3E)%20%3D%3E%20%7B%0D%0A%20%20const%20internalData%20%3D%20data%20as%20InternalValidated%3B%0D%0A%20%20internalData.value.toLowerCase()%3B%0D%0A%20%20%2F%2F%20do%20some%20processing...%0D%0A%20%20return%20data%3B%0D%0A%7D%0D%0A%0D%0Aconst%20initialData%20%3D%20makeFormData(%22test%22)%3B%0D%0Aconst%20validatedData%20%3D%20validate(initialData)%3B%0D%0A%0D%0A%0D%0A%2F%2F%20validate(%22hello%22)%20%2F%2F%20Type%20'%22hello%22'%20is%20not%20assignable%20to%20type%20'%7Bvalue%3A%20never%7D'%0D%0A%2F%2F%20validate(%7Bvalue%3A%20%22hello%22%7D)%20%2F%2F%20Type%20'string'%20is%20not%20assignable%20to%20type%20'never'%0D%0A%0D%0Aconst%20upperCasedValue%20%3D%20upperCase(initialData)%3B%0D%0A%0D%0A%20%20%2F%2F%20process(initialData)%3B%20%2F%2F%20Error!%20Type%20'%22Unvalidated%22'%20is%20not%20assignable%20to%20Type%20'%22Validated%22'%0D%0A%0D%0Aif%20(validatedData%20!%3D%3D%20null)%20%7B%0D%0A%20%20%2F%2F%20validate(validatedData)%3B%20%2F%2F%20Error!%20Type%20'%22Validated%22'%20is%20not%20assignable%20to%20Type%20'%22Unvalidated%22'%20%20%0D%0A%20%20%2F%2F%20upperCase(validatedData)%20%2F%2F%20Error!%20Type%20'%22Validated%22'%20is%20not%20assignable%20to%20Type%20'%22Unvalidated%22'%0D%0A%20%20process(validatedData)%3B%0D%0A%7D%0D%0A)。

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)