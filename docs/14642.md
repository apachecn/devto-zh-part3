# 嵌套条件运算符

> 原文：<https://dev.to/avalander/nested-conditional-operators-611>

*Unsplash*上由 [Talles Alves](https://unsplash.com/photos/HNiOq7eg8ck?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的封面图片

互联网智慧说嵌套的条件运算符是邪恶的。而我直到今天都坚信互联网智慧。

今天，我发现自己正在重构一些旧的 Javascript 代码，它有一个功能可以验证是否存在属性检查，以及对象中的格式是否正确。然后我就疯了，写了类似这样的东西。

```
const validateInput = ({ field1, field2, field3 }) =>
    (!field1
        ? Promise.reject('Missing field1')
        : !Array.isArray(field2)
        ? Promise.reject('field2 is not an array')
        : !isValidType(field3)
        ? Promise.reject('field3 is invalid')
        : Promise.resolve()
    ) 
```

不要介意承诺，这个函数用在承诺链中。

问题是，这让我有点畏缩，因为你知道，*嵌套的条件操作符是邪恶的*，但是我实际上发现它是可读的，并且我要说它甚至可能比 if 链更流畅。

我想看看你在题目中的想法和观点。嵌套的条件操作符本质上是邪恶的和不可读的吗？或者它们是好的，它们只是被以一种混乱的方式使用了太久？