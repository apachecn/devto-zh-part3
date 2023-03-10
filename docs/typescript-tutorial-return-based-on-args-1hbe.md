# TypeScript 教程-基于参数返回

> 原文：<https://dev.to/aexol/typescript-tutorial-return-based-on-args-1hbe>

您好，这是高级 typescript 教程系列的第三篇文章。今天我将讲述通用函数的基本用法

```
type Point = {
  x?: number
  y?: number
  z?: number
}
const myFunc = <T extends Point>(args: T): T => {
  return args
} 
```

Enter fullscreen mode Exit fullscreen mode

作为参数，我将提供一个包含点属性的对象。该函数将根据`args`参数中提供的参数仅返回`Point`的一部分；

```
const result = myFunc({
  x: 1,
  y: 1,
}) 
```

Enter fullscreen mode Exit fullscreen mode

此类函数的智能感知为
[![Typescript return correct args](img/0a442a6511d084e4a79d3732e3753a9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--13UMtFyT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wd6g9ewwqjnnqd3v99b3.png)

如您所见，这里没有`z`属性。Typescript 已经知道我们提供了这两个参数，它应该只返回它们！

这部分非常简短，因为我可以提供无限数量的通用函数用法。