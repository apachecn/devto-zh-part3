# 打字技巧第一部分

> 原文：<https://dev.to/nickytonline/typescript-tips-part-i-4hhp>

我们将从另一篇博文的通常无耻的插入开始。如果你还没有读过，看看我的博客，[考虑使用 TypeScript](https://dev.to/nickytonline/why-you-might-want-to-consider-using-typescript-6j3) 。

我们将会看到一些提示，这些提示可能会对您的打字之旅有所帮助。

首先，如果您正在将 React 应用程序迁移到 TypeScript，让我们从一些需要记住的事情开始。

*   将组件移植到 TypeScript 时，确保文件扩展名是`.tsx`，而不是`.ts`。如果你不这样做，你会为为什么 JSX 没有被认出来而挠头几个小时。

*   此外，还要确保正确设置了`"jsx"` TypeScript 编译器选项。默认设置为`"preserve"`。你希望它被设置为`"react"`。例如[https://github . com/Nicky tonline/www . iam developer . com/blob/master/ts config . JSON # L12](https://github.com/nickytonline/www.iamdeveloper.com/blob/master/tsconfig.json#L12)

液体错误:内部

*   创建一个可重复使用的`children`道具，并通过交集将其添加到您的组件道具类型中。

```
 // some file housing the ChildrenProp
   export type ChildrenProp = { children: React.ReactNode };

   // some other component file that consumes it.
   export type SomeComponentProps = ChildrenProp & {
       someOtherProp: boolean;
       ...
   }; 
```

Enter fullscreen mode Exit fullscreen mode

*   加分，使其具有默认的通用性，[https://github . com/Nicky tonline/www . iam developer . com/blob/master/types/children-prop . d . ts](https://github.com/nickytonline/www.iamdeveloper.com/blob/master/types/children-prop.d.ts)

好吧，让我们到反应之地的外面。

*   如果你还不确定你正在建造的东西是什么形状，或者你正在消费的东西由于某种原因你不知道它的形状，你将把它打成`any`直到你开始弄清楚事情。如果您使用的是 TypeScript 3.0 及更高版本，请不要这样做。首选`unknown`型。

你得到了`any`类型的所有好处，但是一旦你试图访问对象上的任何东西，你将需要做一个类型断言。更多信息，请参见 [`unknown`](https://blogs.msdn.microsoft.com/typescript/2018/07/30/announcing-typescript-3-0/#the-unknown-type) 类型的文档

> ![Nick Taylor profile image](img/7273db47946432a57fe4592fd079a4f7.png)[@ Nicky tonline](https://dev.to/nickytonline)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ Jared Palmer](https://twitter.com/jaredpalmer)另外如果你想增加自己的防御，可以考虑用‘未知’代替‘任何’。[blogs.msdn.microsoft.com/typescript/201…](https://t.co/oJie92UGtg)01:33am-2019 年 1 月 23 日[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1087886002063138816)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1087886002063138816)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1087886002063138816)

这里有一个 [TypeScript Playgound](https://www.typescriptlang.org/play/#src=type%20SomeType%20%3D%20%7B%0D%0A%20%20noYolo%3A%20string%3B%0D%0A%20%20dontNo%3A%20string%3B%0D%0A%7D%0D%0A%0D%0A%2F%2F%20unknown%20complains%20about%20everything%0D%0Aconst%20someObject%3A%20unknown%20%3D%20%7B%0D%0A%20%20noYolo%3A%20'hi'%2C%0D%0A%20%20dontNo%3A%20'%3F'%0D%0A%7D%0D%0A%0D%0Aconsole.log(someObject.noYolo%20!%3D%3D%20undefined)%3B%20%2F%2F%20TS%20Complains%2C%20no%20dice%0D%0A%0D%0A%2F%2F%20We're%20sure%20of%20the%20shape%2C%20or%20some%20other%20kind%20of%20type%20checking%20done%20here%0D%0Aconsole.log((%3CSomeType%3EsomeObject).noYolo)%3B%0D%0A%0D%0A%2F%2F%20any%20on%20the%20other%20hand...%0D%0Aconst%20someOtherObject%3A%20any%20%3D%20%7B%0D%0A%20%20yolo%3A%20'yolo!'%0D%0A%7D%0D%0A%0D%0A%2F%2F%20YOLO%0D%0Aconsole.log(someOtherObject.yolo)%3B%0D%0A%0D%0A) 的例子，如果你想看它的运行。

*   有时你知道有些代码无论如何都会存在，所以你不想检查它是否为空或未定义。TypeScript 允许你通过`!`操作符说，“嘿，TypeScript，相信我，我知道我在做什么。”。

例如，代替这样做的是

```
const someElementReference = document.querySelector('.awesome-selector');

if (someElementReference) {
  someElementReference.setAttribute('data-i-checked-first', `I wasn't sure if you'd exist`);
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样做

```
const someElementReference = document.querySelector('.awesome-selector');

someElementReference!.setAttribute('data-yolo', `I know what I'm doing!`); 
```

Enter fullscreen mode Exit fullscreen mode

少用这个因为这个 giphy。

[![I got the power! Bruce Almighty movie gif](img/ec94f5511330c579b0b79630ffeca8e5.png)](https://i.giphy.com/media/A9grgCQ0Dm012/giphy-downsized.gif)

这就是现在，直到第二部分。我说了，所以我需要现在写。😉