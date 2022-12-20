# TIL: Array.from 有第二个参数

> 原文：<https://dev.to/stefanjudis/til-array-from-has-a-second-argument-1h78>

这是一封相当快的邮件。我关注了谷歌的苏尔马和安德里亚·贾马尔奇在推特上的对话，安德里亚提到`Array.from`接受第二个论点。我以前听过几次这个事实，但总是又忘记了。希望下次我想创建和转换数组的时候能记住这个事实。

```
console.log(Array.from([1, 2, 3], x => x + x));
// [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

他们讨论的片段展示了调用 a `callback` X 次的功能。

```
// call a callback `length` times
Array.from({length}, () => callback())

// or generate a random emoji Array
Array.from(
  {length: 7},
  (v, i) => String.fromCodePoint(
    129300 + Math.floor(Math.random() * 20)
  )
);

// [ '🤡', '🤗', '🤥', '🤛', '🤤', '🤦', '🤔' ] 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 MDN 上阅读更多关于`Array.from` [的内容。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from)