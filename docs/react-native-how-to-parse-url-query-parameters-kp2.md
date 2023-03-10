# React Native:如何解析 url 查询参数

> 原文：<https://dev.to/kapilgorve/react-native-how-to-parse-url-query-parameters-kp2>

[![](img/1f637a3914ca2177794c12b897aa89f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YQV1Jmoh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.ap-south-1.amazonaws.com/kapilgorve/blog/covers/React-Native-parse-url-query-params.jpg) (图片鸣谢:unsplash.com)

## TLDR 解

```
let url = 'AppName://redirect/subpart/?token=123&refreshToken=123'
let regex = /[?&]([^=#]+)=([^&#]*)/g,
  params = {},
  match
while ((match = regex.exec(url))) {
  params[match[1]] = match[2]
  console.log(match[1], match[2])
}
const { token, refreshToken } = params 
```

如果你有一个截止日期(而我们没有？呃)也不在乎解释。复制片段并解决您的问题。我不明白这里发生了什么，所以在我完成最后期限后，我懒得写博客了。问题是我不太了解正则表达式。没有例子，我无法从头开始写，甚至我的生命都依赖于它。作为一名 junir deve，我被告知要避免使用它们，我听从了这个建议。所以我们在这里。

## 问题

说到 url 解析，我使用浏览器 api 中的`URL`接口。为了让搜索参数出现在 url 中，我会这样做:

```
// https://some.site/?id=123
var parsedUrl = new URL(window.location.href)
console.log(parsedUrl.searchParams.get('id')) // 123 
```

这个片段将解决前端应用程序中的常见问题。在 React Native thoguh 中没有这样的对等词。所有的谷歌搜索都来自第三方库。或者堆叠包含复杂正则表达式的 Overlflow 答案来处理这个问题。我对正则表达式感到不舒服。据我所知，如果没有上下文，它们很难调试。我也有同感。

我可以使用字符串方法。当我只需要得到 pne 参数时，我做到了。在添加了一些重要的参数之后。string 方法感觉更像是一个黑客，而不是一个合适的解决方案。也有可能以后会改变 url 的顺序，这会破坏已发布的应用程序功能。这意味着每次序列改变时都要添加一个新的版本，并打破改变。对于 React Native，我非常谨慎地为琐碎的事情添加第三方库。经过一天的搜索，我找到了这个解决方案。遗憾的是，当我写这篇文章的时候，我找不到原文，所以无法回答这个问题。

## 解说

我们在这里定义一种模式。这种模式将以`&`字符开始。因为我们的网址参数将在`&`之后。

`regex.exec(url)``.exec()`功能将搜索`url`中定义的模式`regex`。

为什么我们要使用 while 循环？

这将在每次执行时返回每个匹配。如果你只有一个参数，使用循环是没有意义的。我需要两个参数，所以我在这里使用了一个`while`循环。返回的匹配项的格式为 match[0] =匹配的字符串，match[1]=键，match[2]=值。

```
let url = 'AppName://redirect/subpart/?token=123&refreshToken=123';
      let regex = /[?&]([^=#]+)=([^&#]*)/g,
      params = {},
      match;
      match = regex.exec(url);
      console.log(match);

Output:
  [
    0: "?token=123"
    1: "token"
    2: "123"
    groups: undefined
    index: 27
    input: "AppName://redirect/subpart/?token=123&refreshToken=123"
    length: 3
    __proto__ : Array(0)
  ] 
```

`params[match[1]] = match[2]`我们将匹配的字符串作为键值对存储在`params`中。这只是另一种奇特而又充满活力的方式来写`params.token=match[2]`。这样我们就给`params`写了一个动态密钥。第一次这将为我存储一个`token`属性，第二次存储`refreshToken`。

最后，我使用 [ES6 析构语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)将两个属性提取为单独的变量。

`const { token, refreshToken } = params`

本帖最初发布于[https://www . jskap . com/blog/React-Native-parse-URL-query-params/](https://www.jskap.com/blog/React-Native-parse-url-query-params/)

👋嗨！我是卡皮尔。我总是喋喋不休地谈论建造东西，分享我的知识，自由职业。来 https://twitter.com/kapilgorve 和我打招呼吧