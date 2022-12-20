# 在 TypeScript 中以编程方式获取对象 1 的下级类型

> 原文：<https://dev.to/acro5piano/get-object-1-level-down-type-2ce1>

我们希望获得对象值类型。

假设这个对象:

```
const query = {
  getUserAndBook: {
    user: {
      name: 'acro5piano'
    },
    book: {
      name: 'acro5piano'
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想以编程的方式获取类型`query.getUserAndBook`，我们可以这样写:

```
type Query = typeof query.getUserAndBook 
```

Enter fullscreen mode Exit fullscreen mode

但是如果`query`(这次是`getUserAndBook`)的键可能会变化，那么如何挑选它的类型呢？

...我们可以做:

```
export type GetObjectOneLevelDownType<T extends {}, K extends keyof T> = {
  value: T[K]
}['value'] 
```

Enter fullscreen mode Exit fullscreen mode

诀窍是我们显式地设置`value`属性并选择它。

以下简单得多的代码不起作用:

```
type GetObjectOneLevelDownType<T extends {}, K extends keyof T> = T[K] 
```

Enter fullscreen mode Exit fullscreen mode