# 我们应该如何缩进“const”声明？

> 原文：<https://dev.to/kenbellows/how-should-we-indent-const-declarations-537d>

我非常喜欢在 JavaScript 中添加`let`和`const`来声明块范围的变量。特别是，我喜欢`const`，因为它给了开发者清晰的意图。如果这是一个永远不应该改变的价值观，那就把它变成一个`const`，这样每个人(包括你未来的自己)都知道不要改变它。

但是我有一个抱怨:`let`和它的前身`var`都是 3 个字符， **`const`是 5 个字符**，这破坏了缩进！

```
// var is 3 characters, which aligns nicely with a 4-space indentation style
var foo = 10,
    bar = 20,
    baz = 50;

// let keeps to the same 3 character pattern
let apples = 5,
    oranges = 30, // gotta get that citrus, no scurvy for me
    bananas = 12;

// const ruins everything!!!
const dontchangeme = 10,
    howmuchindentshouldihave = 4,
    thisfeelsverydirty = 0x0; 
```

如果你习惯于 4 个空格的缩进，那么在声明过程中不允许变量名很好的对齐！我不喜欢上面的默认设置，只敲回车，让缩进落在它想落的地方，但我不知道该怎么办。以下是我正在考虑的三个选择:

*   **缩进 6 个空格用于`const`声明**——这是*好的*，但是我讨厌它破坏了与周围代码的一致性。

```
const foo = 10,
      bar = 20,
      baz = 30;
if (test) {
    console.log('Misaligned! 😢')
} 
```

*   **反复写`const`**——我的一些同事采用了这种模式；我没被说服。对我来说，这似乎是不必要的冗长。

```
// seems like a lot of keywords...
const foo = 10;
const bar = 20;
const baz = 30; 
```

*   **给`const`一条自己的线**——我用了一点，还好，但是还是感觉不舒服。

```
const
    foo = 10,
    bar = 20,
    baz = 30; 
```

我需要一些反馈。你用过什么？这些中你喜欢/讨厌哪一个？

我的另一个想法是，现在有一群开发人员在处理“声明比其他任何东西多缩进 2 个空格”的问题:缩进 2 个空格的人！对此你们都做了些什么？你只是缩进 4 个空格来声明，还是...还有别的吗？

等待回复！