# 在大约五分钟的工作和十行代码中，我们将 vendor.js 从 210kb 减少到了 16kb

> 原文：<https://dev.to/ben/we-reduced-our-vendorjs-from-210kb-to-16kb-in-about-five-minutes-of-work-and-ten-lines-of-code-1ole>

即使我们努力在 [dev.to](https://dev.to/) 上实现最小的 JavaScript 负载，我们在优化上也变得懒惰了。我们的`vendor.js`文件，包括所有来自外部库的代码，已经膨胀到 210kb。根据上下文的不同，我们交付的 JavaScript 的总大小已经达到大约 250-300kb。

我们异步加载所有 JavaScript，对于大多数标准来说，这个文件 210kb 已经足够了。所以用户体验没有受到很大影响，但是在不需要这样的时候让这种情况继续下去是很愚蠢的。

我决定最终投入一些脑力来解决这个问题，解决方法非常简单。我个人曾犹豫过要不要研究这个问题，因为我有点不确定这是不是正确的方法。但做完这些后，我觉得很有信心。

一个巧妙的技巧是使用一种叫做“动态导入”的技术来加载这些库。

```
// before
import { bark } from "./dog";
bark("Hello World");

// after
import("./dog").then(({ bark }) => {
  bark("Hello World");
}); 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章精彩地概述了这一技巧和其他一些伟大的技巧:

[![goenning](img/effc620e11024fc10ad3b1c8fc49bbc4.png)](/goenning) [## 我们如何将最初的 JS/CSS 大小减少 67%

### 吉尔姆开口

#webpack #webdev #react #javascript](/goenning/how-we-reduced-our-initial-jscss-size-by-67-3ac0)

通过这样做，只有当用户在我们的代码中触发一个动作时，一些很少使用的库才会被调用。我们的`vendor.js`降低到大约 16kb，我们的总 JavaScript 影响最初大约是 65-75kb，并且可以根据上下文慢慢增加。我相信我们可以让初始负载低于 25kb，但我认为这将需要超过五分钟。如前所述，JavaScript 是延迟的，所以它从来不充当阻塞资源。

这种技术已经在代码的一个区域中就位，但是我们还没有在这些重要的大赢环境中实现它。通过将一些昂贵的库转移到这种技术上，并在我们的代码库中建立更多的模式，我相信我们已经为自己提供了最好的性能体验，并避免了将来出现这样的问题。

这里是实施变更的 PR 。

快乐编码。