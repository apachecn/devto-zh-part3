# Chrome 74 有什么新功能

> 原文：<https://dev.to/bnevilleoneill/what-s-new-in-chrome-74-4g6f>

### Chrome 74 的新功能

[![](img/21a57058da1226a86ff94768561cc977.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uKGbZyg---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/512/1%2A8mHGo6pB8TFW9BLiP-MGbQ.png)

Chrome 74 已经到来，虽然从面向用户的角度来看没有太多令人兴奋的东西，但对有开发意识的人来说还是有一些好处的。新版本中新增了 Javascript 的私有类字段，允许用户减少动画的媒体查询，Windows 的黑暗模式等等。

### **公类领域，遇私类领域**

你可能还记得 Chrome 72 在一月份增加了对 Javascript 新的公共类字段语法的支持。这是一种简化语法的好方法，允许您直接在类定义中定义类字段，不需要构造函数。

现在在 Chrome 74 中，私有类字段加入了它们的公共表亲。私有类字段的功能大致相同，但是使用#来表示它们是私有的还是公共的，当然，它们只能在类内部访问。

复习一下，公共类字段看起来像这样:

```
class IncreasingCounter {
  // Public class field
  _publicValue = 0;
  get value() {
    return this._publicValue;
  }
  increment() {
    this._publicValue++;
  }
} 
```

私有类字段添加了#:

```
class IncreasingCounter {
  // Private class field
  #privateValue = 0;
  get value() {
    return this.#privateValue;
  }
  increment() {
    this.#privateValue++;
  }
} 
```

### **没那么快**

事实证明，有些人并不喜欢一些现代网站上的浮华动画。事实上，视差滚动、缩放和跳动的运动效果会使一些运动不舒服。想象一下在浏览网站的时候晕车。不好玩。操作系统已经开始增加选项来减少这种运动，现在有了 Chrome 74，你可以使用媒体查询，偏好-减少运动，来设计这些人。

这是如何工作的？假设你有一个动画按钮。

你可以像这样使用@ media(prefers-reduced-motion:reduce):

```
button {
  animation: vibrate 0.3s linear infinite both;
}
@media (prefers-reduced-motion: reduce) {
  button {
    animation: none;
  }
} 
```

现在，当有人在 MacOS 或另一个操作系统中打开减少运动偏好时，他们将看不到动画。

### **监听 CSS 转换事件**

好消息，各位！您现在可以监听 CSS 转换事件，如 transitionrun、transitionstart、transitionend 和 transitioncancel。其他浏览器支持这个已经有一段时间了，但是迟做总比不做好。如果您想在转换运行时跟踪或更改行为，那么监听这些事件会很有用。

只是一小段代码……

```
element.addEventListener(‘transitionstart’, () => {
  console.log(‘Started transitioning’);
}); 
```

瞧啊。您正在您的网站上记录过渡。

你能用这个做什么？嗯，也许你的网站上有一个引人注目的动画来吸引用户的注意力。它运行后，他们被迷住了，你想传递一个重要的信息。你怎么能这样做？过渡事件(transitionend)！

### **通过功能策略 API 进行控制**

Chrome 的新功能政策使得启用、禁用或修改 API 和其他网站功能的行为变得容易。有了它们，你可以做一些事情，比如允许 iframes 使用全屏 API，或者改变手机和第三方视频自动播放的默认行为。您可以通过 Feature-Policy 头或 iframe 的 allow 属性来利用这一新功能:

```
HTTP Header: Feature-Policy: geolocation ‘self’
<iframe … allow=”geolocation self”></iframe> 
```

要更深入地了解特性政策，请看一下谷歌关于这个主题的文章。

### **拥抱黑暗模式**

或者不要。重点是现在你可以选择。在 Chrome 73 中，为 Mac 用户添加了黑暗模式，但 Windows 没有。Chrome 74 也开始在 Windows 上推出。与 Mac 版本一样，Windows 中的黑暗模式看起来有点像隐姓埋名模式，新标签页、书签栏等应用了不同的主题。

根据谷歌的说法，这将会逐渐发生，所以如果你还不能完全做到，不要担心。黑暗模式来了。

### **还有什么？**

这些只是 Chrome 74 的部分亮点。如果你正在寻找真相，请访问谷歌官方网站[chromestatus.com](https://www.chromestatus.com/features#milestone%3D74)，了解所有 Chrome 更新。他们深入研究了这些特性，甚至让你先睹为快，看看未来的版本。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[Chrome 74 的新功能](https://blog.logrocket.com/whats-new-in-chrome-74-6f8b82919c68/)首先出现在[的博客](https://blog.logrocket.com)上。