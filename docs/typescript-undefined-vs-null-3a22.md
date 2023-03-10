# TypeScript -未定义 vs null

> 原文：<https://dev.to/andreasbergqvist/typescript-undefined-vs-null-3a22>

所以在 JavaScript 世界中,`undefined`对`null`的讨论可能看起来很古老。但是对我来说，当我转向打字稿的时候，这个问题又出现了。

当我试图阅读关于这个问题的帖子时，似乎有 3 个不同的“阵营”...

## TL；速度三角形定位法(dead reckoning)

我会尽量不使用`null`而只使用`undefined`。

我读了很多已经这样做的人的评论，他们似乎很乐意尝试一下。

优点:

*   “更容易”的语法`let variable?:Type;` vs `let variable:Type = null`或`let variable?:Type = null`；
*   大多数其他语言对此似乎只有一种类型！
*   代码不需要同时处理这两者。

骗局

*   写得更久
*   实际`undefined`变量与设置为`null`的变量之间的差异丢失。
*   JSON 只能处理`null`，不能处理`undefined`。

## 阵营 1 -两者都用

因此，另一种选择绝对是两者都用。这是我以前做的。
但是，正如 TL 中提到的；DR，大多数其他语言似乎都不需要这两者，作为程序员，您可能需要处理未定义和空变量，这会使代码更加复杂。

如前所述，优点是现在实际的`undefined`变量和设置为`null`的变量之间有区别。

```
const [value, setValue] = useState<number| null>(); 
let value?:number | null;
// value = number | null | undefined 
```

## 阵营 2 -使用`null`

`null`是一个在其他语言中用的比较多，写起来比较短的词！Is 也可以很好地与 JSON APIs 一起工作。

所需的额外代码非常值得区分。

```
const [value, setValue] = useState<number| null>(null); 
let value:number | null = null;
// value = number | null 
```

## 阵营 3 -使用`undefined`

应该不需要两个类型，`undefined`是不设置值时的默认类型。

数组函数是 JavaScript 返回的未定义函数。

```
const [value, setValue] = useState<number>(); 
function (value?:number) {}
// value = number | undefined 
```