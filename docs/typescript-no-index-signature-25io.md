# Typescript:没有索引签名

> 原文：<https://dev.to/scottw/typescript-no-index-signature-25io>

我花了一些空闲时间涉猎打字稿。

最近有一件事让我措手不及。我有一个简单的对象，看起来像这样:

```
const unitsOfTime = {
  millisecond: 1,
  second: 60,
  hour: 60 * 60,
  day: 24 * 60 * 60,
  month: 30 * 24 * 60 * 60,
  year: 365 * 24 * 60 * 60
} 
```

在我最初的测试中，我是这样使用它的:`unitsOfTime.day`一切正常。然而，一旦我切换到更动态的东西(`unitsOfTime[someParameter]`)，Typescript 编译器就开始抱怨缺少*索引签名*。

简单搜索了一下，就被引到了这个[PR](https://github.com/Microsoft/TypeScript/pull/7029)<sup id="fnref1">T3】1T5。</sup>

> 使用此 PR，如果对象文字中的所有已知属性都可以分配给索引签名，则对象文字类型可以分配给具有索引签名的类型。这使得将使用对象文字初始化的变量作为参数传递给需要映射或字典的函数成为可能

事后看来，这是有道理的。不能保证这个字典会在这个键 <sup id="fnref2">[2](#fn2)</sup> 返回一个数字。

修复方法是为字典提供一个类型签名:`{[unit: string] : number}`

下面是完整的对象:

```
const unitsOfTime : {[unit: string] : number} = {
  millisecond: 1,
  second: 60,
  hour: 60 * 60,
  day: 24 * 60 * 60,
  month: 30 * 24 * 60 * 60,
  year: 365 * 24 * 60 * 60
  } 
```

* * *

1.  由安德斯·海尔斯伯格！我已经离开微软生态系统有一段时间了，所以在 Github 和 Anders 自己的 PR 上看到这个仍然让我震惊。 [↩](#fnref1)

2.  这对于接收字典作为参数的函数来说更有意思(正如 PR 描述中那样)。在这种特定的情况下，它有助于调用所期望的内容，但是它仍然不能保证`unitsOfTime`总是返回一个数字(如果缺少键，它仍然可能返回一个 null。尽管如此，这是朝着正确方向迈出的一步。 [↩](#fnref2)