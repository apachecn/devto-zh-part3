# 关于类型脚本的注释:类型级编程第 1 部分

> 原文：<https://dev.to/busypeoples/notes-on-typescript-type-level-programming-in-typescript-part-1-i57>

### 简介

这些注释应该有助于更好地理解`TypeScript`，并且在需要查找在特定情况下如何利用 TypeScript 时可能会有帮助。所有示例都基于 TypeScript 3.2。

### 类型等级

我们将定义几个有用的类型，在使用 TypeScript 时可以利用它们。由于这是对该主题的介绍，我们将专注于编写我们自己的类型，然后用 TypeScript 中的现有实现替换它们。这种方法将帮助我们探索高级特性，并了解它们是如何实现的。在本系列的下一部分中，我们将利用所获得的知识来构建更多的定制类型。

#### 只读

有时我们希望确保一个值不被覆盖。这可以通过使用`readonly`来实现，例如我们可能想要保证一个特定的对象属性应该是只读的，我们可以在 TypeScript:
中这样做

```
type User = {
  readonly id: number;
  name: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

但是有时候我们希望确保一个类型是不可变的，而不仅仅是特定的属性。为了解决这个问题，我们可以编写自己的`MakeReadOnly`类型定义，接受一个类型并确保新定义的类型是只读的。

```
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

我们上面定义的`MakeReadOnly`类型严格地说是为了学习，因为 TypeScript 提供了`Readonly`，我们可以利用它来实现与上一个示例相同的结果。

```
type ReadOnlyUser =  Readonly<User>; 
```

Enter fullscreen mode Exit fullscreen mode

#### 部分/必需

有些情况下，我们可能想要转换一个给定的对象，或者需要确保所有的属性都是必需的，但是期望对象具有可选的属性。我们可以编写自己的功能来确保我们可以将一个类型定义从 partial 转换成 required，反之亦然。
接下来，我们来写一些类型。

```
type MakePartial<Type> = { [key in keyof Type]?: Type[key] };
type MakeRequired<Type> = { [key in keyof Type]-?: Type[key] };

// Test MakePartial and MakeRequired
type BlogPost = {
  id: number;
  title: string;
  description?: string;
}

type PartialBlogPost = MakePartial<BlogPost>;

/*
type PartialBlogPost {
  id?: number | undefined;
  title?: string / undefined;
  description?: string / undefined;
}
*/

type RequiredBlogPost = MakeRequired<BlogPost>;

/*
type RequiredBlogPost {
  id: number;
  title: string;
  description: string;
}
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个我们应该注意的有趣的方面，在我们的`MakeRequired`中使用`-?`确保我们移除任何选项，我们可以使用`+`或`-`来获得对类型修饰符的控制，查看[这个答案](https://stackoverflow.com/a/52417260)以获得更多信息。

同样，我们上面定义的`MakePartial`和`MakeRequired`可以用 TypeScript 自己的`Partial`和`Required`代替，这将导致与我们前面的例子相同的结果。

```
type PartialBlogPost = Partial<BlogPost>;

/*
type PartialBlogPost {
  id?: number | undefined;
  title?: string / undefined;
  description?: string / undefined;
}
*/

type RequiredBlogPost = Required<BlogPost>;

/*
type RequiredBlogPost {
  id: number;
  title: string;
  description: string;
}
*/ 
```

Enter fullscreen mode Exit fullscreen mode

#### 挑选、排除和省略

有些情况下，我们需要从现有的类型中创建一个类型，并且可能需要选择或删除一些已定义的属性。

在实现我们的`MakePick`类型之前，让我们看看如何提取两个类型之间的相交键。

```
type MakeIntersect<T, U> = T extends U ? T: never;

// Test MakeIntersect
type User = {
  id: number;
  name: string;
  title: string;
}

type Profile = {
  id: number;
  title: string;
  url: string;
}

type UserProfile = MakeIntersect<keyof User, keyof Profile>;

/*
type UserProfile = "id" | "title";
*/ 
```

Enter fullscreen mode Exit fullscreen mode

现在我们知道了如何找到两个提供的类型之间的交叉键，我们可以为一个提供的类型选择键。

```
type ProfileSelectedKeys = MakeIntersect<
  keyof Profile,
  "id" | "nonExistingKey" | "title"
>;

/*
type ProfileSelectedKeys = "id" | "title";
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这也意味着我们可以写一个类型，它可以选择相交的键并返回一个新的类型定义。

```
type MakePick<Type, Keys extends keyof Type> = { [Key in Keys]: Type[Key] };

// Test MakePick

type NewProfile = MakePick<Profile, "id" | "title">;

/*
type NewProfile = {
  id: number;
  title: string;
}
*/ 
```

Enter fullscreen mode Exit fullscreen mode

有时我们想在覆盖现有类型时排除特定的属性，所以我们的下一步是定义一个`MakeExclude`类型，它返回所有找不到的键。

```
type MakeExclude<T, U> = U extends T ? never: U;

// Test MakeExclude
type NonExistentKeys = MakeExclude<keyof User, keyof Profile>;

/*
type NonExistentKeys = "name";
*/ 
```

Enter fullscreen mode Exit fullscreen mode

属性`name`在`Profile`中不存在，这意味着我们新定义的类型是`type NonExistentKeys = "name"`。

我们可以用 TypeScript 自带的`Pick`和`Exclude`来替换我们之前定义的`MakePick`和`MakeExclude`。

```
// Test MakeExclude
type NonExistentKeys = Exclude<keyof User, keyof Profile>;

/*
type NonExistentKeys = "name";
*/

type NewProfile = Pick<Profile, "id" | "title">;

/*
type NewProfile = {
  id: number;
  title: string;
}
*/ 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可能希望从类型定义中省略属性。TypeScript 目前没有提供`Omit`类型，但是我们可以使用`Exclude`和`Pick`来实现我们自己的类型。同样，在“关于打字稿的说明”系列的第一部分[中，我们已经实现了我们自己的`Omit`。](https://dev.to/busypeoples/notes-on-typescript-pick-exclude-and-higher-order-components-40cp) 

```
type Omit<T, K> = Pick<T, Exclude<keyof T, K>>;

// Test Omit
type NewProfile = Omit<Profile, "title">;
/*
type NewProfile = {
  id: number;
  url: string;
}
*/ 
```

Enter fullscreen mode Exit fullscreen mode

我们现在应该对如何在 TypeScript 中利用类型级编程有了基本的了解。

在下一部分中，我们将构建更高级的类型，这些类型将有助于使用 TypeScript 时的日常工作。

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)