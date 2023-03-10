# 关于类型脚本的注释:类型级编程第 2 部分

> 原文：<https://dev.to/busypeoples/notes-on-typescript-type-level-programming-part-2-2mof>

### 简介

这些注释应该有助于更好地理解`TypeScript`，并且在需要查找在特定情况下如何利用 TypeScript 时可能会有帮助。所有示例都基于 TypeScript 3.2。

### 类型级别编程

在本系列的这一部分中，我们将继续学习更多关于 TypeScript 中的类型级编程。值得注意的是，为了更好地理解这篇文章，对条件类型以及映射和查找类型的基本理解可能会有所帮助。你可以在这里找到以下帖子:

*   [关于类型脚本的注释:类型级编程第一部分](https://dev.to/busypeoples/notes-on-typescript-type-level-programming-in-typescript-part-1-i57)

*   [关于类型脚本的注释:条件类型](https://dev.to/busypeoples/notes-on-typescript-conditional-types-4bh)

*   [关于类型脚本的注释:映射类型和查找类型](https://dev.to/busypeoples/notes-on-typescript-mapped-types-and-lookup-types-i36)

### 例子

在“关于打字稿的笔记”系列的这一部分中，我们将编写一些例子来巩固我们在该主题上的现有知识。

让我们先写一个定义对象类型的抽象。

```
export type ObjectType<Type> = { [k: string]: Type }; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用这个新创建的来定义一个对象，我们可能不知道所有的属性，但是知道属性类型。

```
const o1: ObjectType<string> = {};
const o2: ObjectType<string> = { name: "Test ObjectType" };
//const o3: ObjectType<string> = {name: 1}; // Error! Type 'number' is not assignable to type 'string'. 
```

Enter fullscreen mode Exit fullscreen mode

我们可以采用与上面相同的方法，定义一个数组类型定义。

```
type ArrayType<Type> = { [k: number]: Type };

const a1: ArrayType<string> = [];
const a2: ArrayType<string> = ["Test 1", "Test2"];
// const a3 : ArrayType<string> = [1, 2]; // Error! Type 'number' is not assignable to type 'string'. 
```

Enter fullscreen mode Exit fullscreen mode

上面的定义只是为了演示，因为内置的`Array`类型已经涵盖了这种情况。但是这个例子展示了我们如何创建自己的类型和类型助手。

可能有这样一种情况，我们可能对通过改变属性类型定义来转换现有对象感兴趣。

```
type MakeRecord<Keys, Type> = { [Key in keyof Keys]: Type };

type User = {
  id: number;
  name: string;
};

type UserPropertiesToString = MakeRecord<User, string>;

/*
  type UserPropertiesToString = {
    id: string;
    name: string;
  }
*/

const r1: UserPropertiesToString = { id: "1", name: "Test" };
// const r2: UserPropertiesToString = { id: 1, name: "Test" }; // Error! Type 'number' is not assignable to type 'string'. 
```

Enter fullscreen mode Exit fullscreen mode

接下来让我们开始构建一些更高级例子。使用类型级编程，我们可以扩展 TypeScript 来构建更方便的类型。

我们可以通过利用现有的`Pick`类型来编写我们自己的`Omit`类型。

```
type Omit<Type, Keys> = Pick<Type, Exclude<keyof Type, Keys>>;

type UseWithOnlyId = Omit<User, "name">;

/*
  type UserWithOnlyId = {
    id: number;
  };
*/ 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，我们可以在其他类型的基础上构建更多的类型。例如，我们可能希望能够转换现有的类型。

```
type MakeOverwrite<T, U> = Omit<T, keyof T & keyof U> & U; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用`MakeOverwrite`来覆盖一个现有的类型。让我们通过一个例子来看看这是如何工作的。

```
type UserWithStringId = MakeOverwrite<
  User,
  {
    id: string;
  }
>;

const userWithStringId: UserWithStringId = {
  id: "1",
  name: "Test"
};

// const userWithStringId2 : UserWithStringId = {
//   id: 1,
//   name: "Test"
// };
// Error! Type 'number' is not assignable to type 'string'. 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可能希望扩展并覆盖现有的类型。

```
type UserWithPoints = MakeOverwrite<
  User,
  {
    id: string;
    points: number;
  }
>;

const userWithPoints: UserWithPoints = {
  id: "1",
  name: "Test",
  points: 0
};

// const userWithPoints: UserWithPoints = {
//   id: "1",
//   name: "Test",
// };
// Error! Property 'points' is missing. 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看看如何继续扩展类型。让我们看看另一个有趣的助手类型，我们可能希望在使用集合时实现它。

```
type GetHeadType<Type> = Type extends [infer H, ...any[]] ? H : never; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可能想推断数组中第一项的类型。通过使用`GetHeadType`,我们可以推断出初始项目的类型。

```
type HeadType = GetHeadType<["Test", 1, { id: 2 }]>; // type HeadType = "Test"
type HeadType2 = GetHeadType<[1, { id: 2 }, "Test"]>; // type HeadType2 = 1
type HeadType3 = GetHeadType<[{ id: 2 }, 1, "Test"]>; // type HeadType3 = {id: 2;} 
```

Enter fullscreen mode Exit fullscreen mode

使用条件类型，我们可以编写逻辑类型。

```
type Not<Type extends boolean> = Type extends true ? false : true; 
```

Enter fullscreen mode Exit fullscreen mode

我们可能会使用`Not`来确保一个类型与所提供的类型相反。

```
type TestNot = Not<true>; // type TestNot = false;
type TestNot2 = Not<false>; // type TestNot = true; 
```

Enter fullscreen mode Exit fullscreen mode

让我们建立更多的例子来巩固我们的知识。

可能有这样一种情况，我们可能希望将所有类型属性转换为 strict。

```
type MakeStrict<Type> = { [Key in keyof Type]-?: Type[Key] };

type Product = {
  id: number;
  amount: number;
  name: string;
  description: string;
  locationId?: number;
};

type StrictProduct = MakeStrict<Product>;

/*
  type StrictProduct = {
    id: number;
    amount: number;
    name: string;
    description: string;
    locationId: number;
  };
*/ 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`-?`,我们可以告诉 TypeScript 删除可选参数。
与严格相反，使类型可选，可以通过使用`+?`来实现。

```
type MakeOptional<Type> = { [Key in keyof Type]+?: Type[Key] };

type OptionalProduct = MakeOptional<Product>;

/*
  type StrictProduct = {
    id?: number;
    amount?: number;
    name?: string;
    description?: string;
    locationId?: number;
  };
*/ 
```

Enter fullscreen mode Exit fullscreen mode

如果我们需要移除所有可选属性，我们可以编写自己的类型来移除所有可选属性。

```
type RequiredKeys<Type> = {
  [Key in keyof Type]: {} extends { [K in Key]: Type[Key] } ? never : Key
}[keyof Type];

type OnlyRequired<T> = Pick<T, RequiredKeys<T>>;

type OnlyRequiredProduct = OnlyRequired<Product>;

/*
  type OnlyRequiredProduct = {
    id: number;
    amount: number;
    name: string;
    description: string;
  };
*/ 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，`{} extends {id?: number}`为真，而`{} extends {id: number}}`为假，这使得能够识别属性是否可选。

```
type OptionalExtendsTest = {} extends {id?: number} ? true: false; // type OptionalExtendsTest = true
type OptionalExtendsTest2 = {} extends {id: number} ? true: false; // type OptionalExtendsTest = false 
```

Enter fullscreen mode Exit fullscreen mode

可能会有这样一种情况，我们希望找出哪些属性属于特定类型。

```
type MakeKeysByType<Type extends {}, SearchType> = {
  [Key in keyof Type]-?: Type[Key] extends SearchType ? Key : never
}[keyof Type];

type ProductPropsByTypeNumber = MakeKeysByType<Product, number>;
// type ProductPropsByTypeNumber = "id" | "amount" 
```

Enter fullscreen mode Exit fullscreen mode

选择特定类型的所有键，但删除任何可选属性。

我们现在应该对如何在 TypeScript 中利用类型级编程有了很好的理解。如果您需要更好地了解基础知识，以下帖子可能会有所帮助:

*   [关于类型脚本的注释:类型级编程第一部分](https://dev.to/busypeoples/notes-on-typescript-type-level-programming-in-typescript-part-1-i57)

*   [关于类型脚本的注释:条件类型](https://dev.to/busypeoples/notes-on-typescript-conditional-types-4bh)

*   [关于类型脚本的注释:映射类型和查找类型](https://dev.to/busypeoples/notes-on-typescript-mapped-types-and-lookup-types-i36)

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)