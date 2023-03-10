# Async-Ray 和 alls 2 promise 实用程序库是用 TypeScript 编写的

> 原文：<https://dev.to/rpgeeganage/async-ray-and-alls-2-promise-utility-libraries-are-written-in-typescript-2676>

大家好，
我想介绍以下两个图书馆。它们是用 100%测试覆盖率的打字稿编写的。

### 异步射线:

这个包的目的是为数组中的`every`、`filter`、`find`、`findIndex`、`forEach`、`map`、`reduce`、`reduceRight`和`some`方法提供`async/await`回调，方便链接。

*   回购:[https://github.com/rpgeeganage/async-ray](https://github.com/rpgeeganage/async-ray)
*   链接样本:
    *   [https://github . com/rpgeeganage/async-ray/blob/master/examples/chain _ sample _ 1 . ts](https://github.com/rpgeeganage/async-ray/blob/master/examples/chain_sample_1.ts)
    *   [https://github . com/rpgeeganage/async-ray/blob/master/examples/chain _ sample _ 2 . ts](https://github.com/rpgeeganage/async-ray/blob/master/examples/chain_sample_2.ts)

### [绝对是:](#alls)

只是另一个图书馆，唯一的目的是等待所有的承诺完成不多不少。因为`Promise.all()`不会等待错误。

*   回购:[https://github.com/rpgeeganage/alls](https://github.com/rpgeeganage/alls)
*   示例:[https://github . com/rpgeeganage/all s/blob/master/test/all s . ts](https://github.com/rpgeeganage/alls/blob/master/test/alls.ts)

谢谢你