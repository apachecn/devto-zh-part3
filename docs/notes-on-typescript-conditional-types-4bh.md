# 关于类型脚本的注释:条件类型

> 原文：<https://dev.to/busypeoples/notes-on-typescript-conditional-types-4bh>

### 简介

这些注释应该有助于更好地理解`TypeScript`，并且在需要查找在特定情况下如何利用 TypeScript 时可能会有帮助。所有示例都基于 TypeScript 3.2。

### 条件类型

在“关于 TypeScript 的笔记”系列的最后一部分中，我们着重于理解类型级编程的基础。在我们继续学习 TypeScript 中更高级的类型级别示例之前，先介绍另一个主题可能是个好主意。在本系列的这一部分中，我们将试图更好地理解什么是条件类型，以及在现有应用程序中使用 TypeScript 时如何利用它们。

在其最基本的形式中，条件类型是一种类型的三元表达式。为了更好地理解，让我们看一个低级的例子。

```
type S = string extends any ? "string" : never;

/*
type S = "string"
*/ 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript 2.8 的[发行说明中有条件类型的定义:](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html)

*“条件类型基于表示为类型关系测试的条件选择两种可能类型中的一种”*

因此，如果我们看看我们最初的例子，我们可能会想到如下的条件类型:

```
T extends U ? X : Y 
```

Enter fullscreen mode Exit fullscreen mode

这意味着如果类型`T`扩展了`U`，那么条件类型将被解析为`X`，否则将被解析为`Y`。起初这可能听起来很抽象，但是我们将在下面的例子中看到这个概念的用处。同样，我们将编写一些通用函数，帮助我们显示条件类型的原因和内容。

```
type Exclude<T, U> = T extends U ? never : T; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们看看上面的类型定义，我们可以看到我们正在使用一个三元表达式来定义返回类型。`never`是一个低级类型，它不能被进一步细化，我们可以利用`never`表示这个值不应该存在的事实，这意味着我们可以过滤掉一个类型，如果它是`never`。

如果我们可以写一个`Exclude`类型，我们也可以定义一个`Include`类型。

```
type Include<T, U> = T extends U ? T : never; 
```

Enter fullscreen mode Exit fullscreen mode

所以只要看看上面的定义，我们就可以注意到我们翻转了返回的类型。有趣的是,`Include`可以替换为`Extract`,这是 TypeScript 中预定义的条件类型，我们自己定义的`Exclude`也是如此，也是预定义的。

现在，我们对条件类型有了更好的理解，让我们用我们目前所学的来构建一个`NonNullable`条件类型，它从一个提供的类型中排除了任何`null`或`undefined`类型。

```
type NonNullable<T> = Exclude<T, null | undefined>;

// Test NonNullable
type TestNonNullable = NonNullable<string | null | number | undefined>;

/*
type TestNonNullable = string | number;
*/ 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们利用了之前定义的`Exclude`类型，但是我们也可以用更明确的方式来编写它。

```
type NonNullable<T> = T extends null | undefined ? never : T;

// Test NonNullable
type TestNonNullable = NonNullable<string | null | number | undefined>;

/*
type TestNonNullable = string | number;
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这正是预定义的`NonNullable`在 TypeScript 中实现的方式。我们检查提供的类型是否扩展了`null`或`undefined`，并返回一个`never`类型或类型本身。

后退一步，尝试更好地理解我们测试`NonNullable` type 时发生了什么，这可能会很有趣。当我们运行前面的例子时，下面的情况适用:

```
type TestNonNullable =
  | NonNullable<string>
  | NonNullable<null>
  | NonNullable<number>
  | NonNullable<undefined>;

/*
type TestNonNullable = string | number;
*/ 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们一直在实现现有的条件类型。为了加深我们对这个主题的理解，让我们看一个例子，在这个例子中，我们可能希望从一个类型中移除所有可为空的属性，并创建一个只需要不可为空的类型的新类型。

```
type RemoveUndefinable<Type> = {
  [Key in keyof Type]: undefined extends Type[Key] ? never : Key
}[keyof Type];

type RemoveNullableProperties<Type> = {
  [Key in RemoveUndefinable<Type>]: Type[Key]
};

type TestRemoveNullableProperties = RemoveNullableProperties<{
  id: number;
  name: string;
  property?: string;
}>;

/*
type TestRemoveNullableProperties = {
  id: number;
  name: string;
};
*/ 
```

Enter fullscreen mode Exit fullscreen mode

`RemoveNullableProperties`类型删除所有可选类型，因为它们可能是未定义的。可以组合映射类型和条件类型来构建高级类型，如上面的示例所示。

### 推断并键入干扰

在我们继续实现更多的条件类型之前，让我们试着更好地理解如何利用条件类型的类型干扰。使用`infer`关键字，我们可以告诉 TypeScript 尝试推断类型。让我们看几个例子，利用`infer`可以帮助我们。

```
type GetFunctionArgumentTypes<Type> = Type extends (a: infer U) => void
  ? U
  : never;

type TestGetFunctionArgumentTypesA = GetFunctionArgumentTypes<
  (a: number) => void
>;

/*
type GetFunctionArgumentTypes = number;
*/

type GetPropertyTypes<Type> = Type extends {a: infer U, b: infer U} ? U : never;

type TestGetPropertyTypes = GetPropertyTypes<{a: number, b: string[]}>;

/*
type TestGetPropertyTypes = number | string[];
*/ 
```

Enter fullscreen mode Exit fullscreen mode

看着上面的例子，我们注意到我们可以将`infer`关键字放在我们想要推断类型的位置。让我们看看能否推断出函数 f.e.
的返回类型

```
type GetReturnType<Type> = Type extends (...a: any[]) => infer R ? R : any;

type TestGetReturnType = GetReturnType<(a: number) => number[]>;

/*
type TestGetReturnType = number[];
*/ 
```

Enter fullscreen mode Exit fullscreen mode

我们的`GetReturnType`可以推断出一个提供的函数的返回类型。这是与提供的 TypeScript 类似的实现`ReturnType`。你可以在之前的“关于类型脚本的注释”文章中读到更多关于 [ReturnType](https://dev.to/busypeoples/notes-on-typescript-returntype-3m5a) 的内容。

最后，我们可以扩展前面的`GetReturnType`来推断构造函数的实例类型。

```
type GetInstanceType<T> = T extends new (...args: any[]) => infer R ? R : any;

type TestGetInstanceType = GetInstanceType<new (a: number) => number | string | undefined>;

/*
type TestGetReturnType = number | string | undefined;
*/ 
```

Enter fullscreen mode Exit fullscreen mode

同样，这个实现类似于 TypeScript 提供的条件类型`InstanceType`。

我们应该很好地理解条件类型，以及在使用 TypeScript 时如何利用它们。在这篇文章中获得的知识应该有助于我们在即将出版的“TypeScript 笔记”中构建更高级的类型级编程示例。

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)