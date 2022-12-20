# 打字技巧第二部分:声明合并

> 原文：<https://dev.to/nickytonline/typescript-tips-part-ii-declaration-merging-5gba>

[声明合并](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)在 TypeScript 中已经存在一段时间了。简而言之，它允许你合并类型的定义。
文档中有很多例子，所以让我们从简单的开始吧。

```
enum HardDriveType {
    ssd,
    sata
}

enum ProcessorType {
    i3,
    i5,
    i7,
    i9
}

interface Computer {
    processor: ProcessorType;
}

interface Computer {
    hardDriveType: HardDriveType;
}

// interface has been merged
const myPC: Computer = {
    hardDriveType: HardDriveType.ssd,
    processor: ProcessorType.i9
};

// interface is merged so type checking fails since the processor property is missing
const myBadPC: Computer = {
    hardDriveType: HardDriveType.ssd,
}; 
```

Enter fullscreen mode Exit fullscreen mode

你可以[在 TypeScript Playground 用示例](https://www.typescriptlang.org/play/#src=enum%20HardDriveType%20%7B%0A%09ssd%2C%0A%09sata%0A%7D%0A%0Aenum%20ProcessorType%20%7B%0A%09i3%2C%0A%09i5%2C%0A%09i7%2C%0A%09i9%0A%7D%0A%0Ainterface%20Computer%20%7B%0A%09processor%3A%20ProcessorType%3B%0A%7D%0A%0Ainterface%20Computer%20%7B%0A%09hardDriveType%3A%20HardDriveType%3B%0A%7D%0A%0A%2F%2F%20interface%20has%20been%20merged%0Aconst%20myPC%3A%20Computer%20%3D%20%7B%0A%09hardDriveType%3A%20HardDriveType.ssd%2C%0A%09processor%3A%20ProcessorType.i9%0A%7D%3B%0A%0A%2F%2F%20interface%20is%20merged%20so%20type%20checking%20fails%20since%20the%20processor%20property%20is%20missing%0Aconst%20myBadPC%3A%20Computer%20%3D%20%7B%0A%09hardDriveType%3A%20HardDriveType.ssd%2C%0A%7D%3B)来玩。
所以声明了两个名为`Computer`的接口，这些接口的所有属性都合并到一个对`Computer`接口的声明中。这是一个简单的例子来展示它是如何工作的，但是在一个真实的应用程序中，你不会在一个文件的两个部分中声明这个接口。让我们用一些更现实的东西。

您正在使用第三方库，或者它是您的项目中需要存在于`window`上的东西。`window`有自己的类型，[窗口接口](https://github.com/Microsoft/TypeScript/blob/master/lib/lib.dom.d.ts#L16513)。这种类型拥有你期望在 MDN 上找到的关于`window`的所有属性。

让我们使用一个名为`awesomeThing`的虚拟第三方库。它被加载到`window`对象上，所以我们需要增强`Window`接口。

```
export interface AwesomeThing {
    doIt: () => void;
}

declare global {
    interface Window {
        awesomeThing: AwesomeThing
    }
}

// The window interface has been merged with our interface to add awesomeThing.
window.awesomeThing.doIt();

// Errors because it's not on the `Window` interface.
window.thingThatIsNotOnWindow.doIt(); 
```

Enter fullscreen mode Exit fullscreen mode

你可以[在 TypeScript 游乐场里摆弄示例](https://www.typescriptlang.org/play/#src=export%20interface%20AwesomeThing%20%7B%0D%0A%09doIt%3A%20()%20%3D%3E%20void%3B%0D%0A%7D%0D%0A%0D%0Adeclare%20global%20%7B%0D%0A%09interface%20Window%20%7B%0D%0A%09%09awesomeThing%3A%20AwesomeThing%0D%0A%09%7D%0D%0A%7D%0D%0A%0D%0A%2F%2F%20The%20window%20interface%20has%20been%20merged%20with%20our%20interface%20to%20add%20awesomeThing.%0D%0Awindow.awesomeThing.doIt()%3B%0D%0A%0D%0A%2F%2F%20Errors%20because%20it's%20not%20on%20the%20%60Window%60%20interface.%0D%0Awindow.thingThatIsNotOnWindow.doIt()%3B)。

如果你想在开源中看到一些真实世界的例子，只要看看我最近的一个 PR 就知道了，它被合并到了[改进的 GitHub](https://github.com/sindresorhus/refined-github/pull/1750) 浏览器扩展库中。我真的很高兴得到这个 PR，因为它是我每天使用的扩展。

> ![Sindre Sorhus profile image](img/509c5ff335f15e26403344259d8c247b.png)Sindre sor HUS[@ Sindre sor HUS](https://dev.to/sindresorhus)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)改进后的 GitHub 浏览器扩展刚刚通过了 1000 次提交，我们也刚刚完成向 TypeScript 的过渡，感谢 [@nickytonline](https://twitter.com/nickytonline) 和 [@bfred_it](https://twitter.com/bfred_it) 👏[github.com/sindresorhus/r…](https://t.co/APJxy0RSHB)2019 年 2 月 08 日下午 15:26[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1093893754992320513)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1093893754992320513)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1093893754992320513)

具体来说，检查项目中的 [globals.d.ts](https://github.com/sindresorhus/refined-github/blob/master/source/globals.d.ts#L78) 。

差不多就是这么多了。总而言之，声明合并是增强现有类型的一个好方法。

在 [Unsplash](https://unsplash.com/search/photos/train-tracks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由[迈克·埃尼奥](https://unsplash.com/photos/2IkxeDKaZdY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片