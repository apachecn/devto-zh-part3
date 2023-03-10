# 关于 TypeScript 的注释:映射类型和查找类型

> 原文：<https://dev.to/busypeoples/notes-on-typescript-mapped-types-and-lookup-types-i36>

### 简介

这些注释应该有助于更好地理解`TypeScript`，并且在需要查找在特定情况下如何利用 TypeScript 时可能会有帮助。所有示例都基于 TypeScript 3.2。

### 映射类型

在“关于 TypeScript 的笔记”系列的这一部分中，我们希望提高我们在 TypeScript 中的类型级编程知识。为了更好地理解这个主题，我们将重温在[“类型级编程第 1 部分”](https://dev.to/busypeoples/notes-on-typescript-type-level-programming-in-typescript-part-1-i57)中讨论的一些主题，并更深入地了解这些类型是如何实现的。更具体地说，让我们更好地理解**映射类型**。

在之前的一篇文章中，我们实现了一个部分类型、一个必需类型和一个只读类型。

```
type User = {
  id: number;
  name: string;
};

type MakeReadOnly<Type> = {readonly [key in keyof Type ]: Type[key]};
// Test MakeReadOnly
type ReadOnlyUser =  MakeReadOnly<User>;

/*
type ReadOnlyUser = {
  readonly id: number;
  readonly name: string;
}
*/ 
```

Enter fullscreen mode Exit fullscreen mode

我们实现了这些类型，但没有更深入地讨论实际的底层机制，这是我们现在要做的。看一下我们的`MakeReadOnly`，我们注意到我们可以映射属性类型来创建一个新的类型。在上面的具体例子中，我们映射了所有的属性类型，并将它们转换为只读的*。*

 *让我们看看我们以前实现的另一个例子。

```
type MakePick<Type, Keys extends keyof Type> = { [Key in Keys]: Type[Key] }; 
```

Enter fullscreen mode Exit fullscreen mode

我们的`MakePick`，反映了提供的`Pick`，遍历所有的键，扩展了提供的`Type`，并返回一个新的类型。

```
type User = {
  id: number;
  name: string;
  points: number;
};
type TestMakePick = MakePick<User, "id" | "name">;

/*
type TestMakePick = {
  id: number;
  name: string;
};
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这就是当我们试图从`User` :
中选择 id 和名称时实际发生的情况

```
type TestMakePick = { [Key in "id" | "name"]: User[Key] };

/*
type TestMakePick = {
  id: number;
  name: string;
};
*/ 
```

Enter fullscreen mode Exit fullscreen mode

使用查找类型，我们将在下一节中更深入地探讨，我们可以将上面的例子重写为:

```
type TestMakePick = {
  id: User["id"],
  name: User["name"]
};

/*
type TestMakePick = {
  id: number;
  name: string;
};
*/ 
```

Enter fullscreen mode Exit fullscreen mode

上面的转换应该有助于我们更好地理解映射类型是如何工作的。

### 查找类型

为了构建更高级的类型，让我们来看看两个有趣的类型脚本特性**查找类型**和**关键字**。

```
type UserKeyTypes = User["id" | "name" | "points"];

/*
type UserKeyTypes = number | string;
*/ 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`keyof`，我们可以将上面的类型重写为:

```
type UserKeyTypes = User[keyof User];

/*
type UserKeyTypes = number | string;
*/ 
```

Enter fullscreen mode Exit fullscreen mode

可以帮助我们避免手动定义所有的键，而是让 TypeScript 提供键，这有助于避免在这些类型发生变化时必须不断更新它们。

```
type UserKeys = keyof User;

/*
type UserKeys = "id" | "name" | "string";
*/ 
```

Enter fullscreen mode Exit fullscreen mode

查找类型，也称为索引访问类型，使我们能够访问所提供的键的类型。类似于我们如何访问对象属性的值，但是对于类型。

```
type UserNameType = User["name"];

/*
type UserNameType = string;
*/ 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以查找一些键，如我们在本节中编写的第一个示例所示:

```
type UserKeyTypes = User["id" | "name" | "points"];

/*
type UserKeyTypes = number | string;
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这是一个有趣的特性，它使开发人员在处理对象属性时能够提供更显式的类型处理。让我们复制一个来自 **Ramda** 库的`prop`和`assoc`函数的更简单的(无 currying)实现。

```
function prop(obj, key) {
  return obj[key]
} 
```

Enter fullscreen mode Exit fullscreen mode

键入`prop`函数的一种方法是提供一个对象类型并将键定义为一个字符串。但是 TypeScript 将无法推断返回类型。

我们需要更加明确键的类型，这可以通过定义:`Key extends keyof Type`来保证键的类型扩展所提供的对象键的类型来实现。

```
function prop<Type, Key extends keyof Type>(obj: Type, key: Key) {
  return obj[key];
}

const user: User = {
  id: 1,
  name: "Test User",
  points: 0
};

const userName = prop(user, "name"); // const userName : string; 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，TypeScript 现在可以推断返回值的返回类型:

```
return obj[key]; // => Type[Key] 
```

Enter fullscreen mode Exit fullscreen mode

利用查找类型和 keyof 的另一个好处是，我们可以确保只有现有的属性键可以传递给`prop`。

```
// The following will result in TypeScript complaining:
const userName = prop(user, "status");
// Argument of type '"status"' is not assignable to parameter of type '"id" | "name" | "points"' 
```

Enter fullscreen mode Exit fullscreen mode

因此，试图访问一个不存在的属性将导致 TypeScript 抱怨所提供的类型是不可赋值的。为了获得更多的理解并验证我们目前所学的知识，让我们来实现`assoc`。

```
function assoc<Type, Key extends keyof Type>(
  obj: Type,
  key: Key,
  value: Type[Key]
) {
  return { ...obj, [key]: value };
} 
```

Enter fullscreen mode Exit fullscreen mode

`assoc`看起来类似于`prop`函数，但是因为我们这次也提供了一个值，所以我们可以使用查找类型通过`Type[Key]`来定义期望的值类型。

```
const updatedUserName = assoc(user, "name", "User Test A");
const updatedUserPoints = assoc(user, "points", 0);

// The following will examples result in TypeScript complaining:

const updatedUserPoint = assoc(user, "point", 0);
// Argument of type '"point"' is not assignable to parameter of type '"id" | "name" | "points"'

const updatedUserPointsAsString = assoc(user, "point", "0");
// Argument of type '"0"' is not assignable to parameter of type 'number' 
```

Enter fullscreen mode Exit fullscreen mode

通过利用查找类型，我们可以保证预期的更新值具有正确的类型。

### 映射类型和查找类型

最后，让我们再看一个我们在使用[条件类型](https://dev.to/busypeoples/notes-on-typescript-conditional-types-4bh)时构建的更高级的例子。

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

`RemoveNullableProperties`条件类型需要一个类型，并返回一个只包含不可空属性类型的新类型。将现有的实现分成多个部分是一个好主意，因为我们现在对映射和查找类型有了更好的理解。

```
type RemoveUndefinable<Type> = {
  [Key in keyof Type]: undefined extends Type[Key] ? never : Key
}[keyof Type]; 
```

Enter fullscreen mode Exit fullscreen mode

先从`RemoveUndefinable`说起:

```
type RemoveUndefinableKeys<Type> = {
  [Key in keyof Type]: undefined extends Type[Key] ? never : Key
};

type TestRemoveUndefinableKeys = RemoveUndefinable<{
  id: number;
  name: string;
  property?: string;
}>;

/*
type TestRemoveUndefinableKeys = {
  id: "id";
  name: "name";
  property?: undefined;
}
*/ 
```

Enter fullscreen mode Exit fullscreen mode

我们得到一个新的类型，它包含键名或者 undefined，这取决于该类型是否扩展了 undefined。如果您还记得，我们可以像这样查找多个类型:`{a: number; b: string; c: number[]}["a" | "b"]`，这意味着我们可以在下一步中使用查找来提取所提供类型的所有相关键。

```
type RemoveUndefinableKeys<Type> = {
  [Key in keyof Type]: undefined extends Type[Key] ? never : Key
};

type RemoveUndefinable<Type> = RemoveUndefinableKeys<Type>[keyof Type];

type TestRemoveUndefinable = RemoveUndefinable<{
  id: number;
  name: string;
  property?: string;
}>;

/*
type TestRemoveUndefinable = "id" | "name" | undefined;
*/ 
```

Enter fullscreen mode Exit fullscreen mode

返回所有映射的键名，所以我们的下一步是从提供的类型中删除任何不存在的键。

```
type RemoveUndefinableKeys<Type> = {
  [Key in keyof Type]: undefined extends Type[Key] ? never : Key
};

type RemoveUndefinable<Type> = RemoveUndefinableKeys<Type>[keyof Type]>;

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

我们可以将`RemoveNullableProperties`与提供的`Pick`类型的类型脚本交换，因为`Pick`的实现类似于`RemoveNullableProperties`的实现:

```
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
} 
```

Enter fullscreen mode Exit fullscreen mode

将`RemoveNullableProperties`与`Pick`交换后，我们得到了下面的实现:

```
type RemoveUndefinableKeys<Type> = {
  [Key in keyof Type]: undefined extends Type[Key] ? never : Key
};

type RemoveNullableProperties<Type> = Pick<
  Type,
  RemoveUndefinableKeys<Type>[keyof Type]
>;

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

我们应该很好地理解映射类型、查找类型和 keyof，以及在使用 TypeScript 时如何利用它们。在这篇文章中获得的知识应该有助于我们在即将到来的“关于 TypeScript 的笔记”中构建更高级的类型级编程示例，这篇文章将重点讨论更高级的类型级编程。

### 链接

[TypeScript 2.1 发行说明](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html#keyof-and-lookup-types)

[TypeScript 2.1:映射类型](https://mariusschulz.com/blog/typescript-2-1-mapped-types)

[类型脚本 2.1: keyof 和查找类型](https://mariusschulz.com/blog/typescript-2-1-keyof-and-lookup-types)

[关于类型脚本的注释:类型级编程第一部分](https://dev.to/busypeoples/notes-on-typescript-type-level-programming-in-typescript-part-1-i57)

[关于类型脚本的注释:条件类型](https://dev.to/busypeoples/notes-on-typescript-conditional-types-4bh)

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)*