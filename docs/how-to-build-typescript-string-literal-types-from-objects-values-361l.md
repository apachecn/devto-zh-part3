# 如何从对象构建 Typescript 字符串文字类型

> 原文：<https://dev.to/multimo/how-to-build-typescript-string-literal-types-from-objects-values-361l>

使用 typescript 3.4，我们可以获得常量断言。这使得从 Javascript 对象创建枚举更加容易。如果没有 const 断言，就很难用一些对象值的字符串来创建类型。然而，用键来做这件事是相当琐碎的。

```
const translations = {
    header_title: "DEV.to",
    footer_cta: "about us",
    header_subtext: "",
};

// "header_title" | "footer_cta" | "header_subtext"
type TranslationsKeys = keyof typeof translations; 
```

但是假设要创建一个基于常量值的类型。例如，在下面的例子中，我们想要路径的值，而不是关键字。

这就是 const 断言发挥作用的地方。在下面的例子中，如果我们没有`as const`,`RoutesEnum`的类型将只是`string`。

```
const Routes = {
    home: "/",
    about: "/about-us",
    cart: "/cart",
};

type ValueOf<T> = T[keyof T];

 // string
type RoutesEnum = ValueOf<typeof Routes>; 
```

有时这就足够了，但对我们的用例来说还不够。const 断言将使所有属性都是只读的，并将它们转换成字符串文字。然后我们可以使用`ValueOf`函数创建一个包含所有值的对象类型。

```
const Routes = {
    home: "/",
    about: "/about-us",
    cart: "/cart",
} as const;

type ValueOf<T> = T[keyof T];

// "/" | "about-us" | "/cart"
type RoutesEnum = ValueOf<typeof Routes>; 
```

[游乐场链接](https://www.typescriptlang.org/play/#src=export%20const%20Colors%20%3D%20%7B%0D%0A%20%20%20%20red%3A%20%22RED%22%2C%0D%0A%20%20%20%20blue%3A%20%22BLUE%22%2C%0D%0A%20%20%20%20green%3A%20%22GREEN%22%2C%0D%0A%7D%20as%20const%3B%0D%0A%0D%0Atype%20ValueOf%3CT%3E%20%3D%20T%5Bkeyof%20T%5D%3B%0D%0Atype%20ColorsType%20%3D%20ValueOf%3Ctypeof%20Colors%3E%3B%0D%0A%0D%0Afunction%20hi(hi%3A%20ColorsType)%20%7B%0D%0A%20%20%20%20return%20hi%0D%0A%7D%0D%0A%0D%0Ahi(Colors.blue))

词汇表:
[字符串文字](https://www.typescriptlang.org/docs/handbook/advanced-types.html#string-literal-types)
[常量断言](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-4.html)
[keyof](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html#keyof-and-lookup-types)