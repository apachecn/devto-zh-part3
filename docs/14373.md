# 动态生成 TypeScript 字符串文字类型

> 原文：<https://dev.to/dance2die/dynamically-build-typescript-string-literal-type-1bc7>

*照片由[凯文·杰瑞特](https://unsplash.com/photos/t3d22GqvUqs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/type-build?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

TypeScript 有[字符串文字类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#string-literal-types)，它允许您指定变量允许的字符串值。

您可以在类型声明时指定允许的文字值。

当你有几十个字符串可以增长时，增加它是不可维护的，也容易出错。

TypeScript 提供了 [keyof](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html) 运算符来提取一种类型的键，以限制可赋值的值。

让我向您展示它的使用方法及其局限性。

## 🚀介绍

我遇到了一个问题，我想限制一个函数的允许值，只接受数组中的 DOM 类型值。

这篇文章是经过[天的挫折](https://spectrum.chat/typescript/general/solved-is-there-a-way-to-create-type-from-an-array~2317e93b-0c66-4088-a4a6-9d6ebbd257ea)后找到的变通方法。

我将向您展示如何创建一个只允许 DOM 类型值的类型。

## 🌠入门指南

您需要创建一个对象(*而不是*一个数组)，其中包含您想要用来限制类型的对象键。

稍后我会告诉你为什么数组不起作用。

假设您有一个以 DOM 作为键的对象。