# 我真的需要一个本地应用程序吗？

> 原文：<https://dev.to/s1hofmann/do-i-actually-need-a-native-app-for-that-2efj>

# TL；DR；

我创建了一个计划扑克 PWA。

这里是[源](https://github.com/s1hofmann/planning-poker)T2，这里是部署的[原型](https://s1hofmann.github.io/planning-poker/)。

# 一点案例分析

当讨论新功能时，我们的分布式团队大量使用规划扑克应用程序。我们每个人都带着手机坐在摄像头前，数到三，每个人都通过敲击屏幕或摇动手机来表达自己的估计。对我们来说，这是一种社交并同时完成工作的好方法。

根据手头的任务，我们使用不同类型的卡片组，但大多数时候不是“*斐波那契*”就是“ *T 恤尺寸*”。

因此，当谈论一个(非常)基本的规划扑克应用程序的功能时，它应该提供以下内容:

1.  提供不同的规划扑克牌供选择
2.  允许用户选择一张卡片
3.  将卡片倒置展示
4.  轻敲屏幕或摇动手机会翻转卡片

听起来相当直截了当，但是…

## 大小要紧？

上次我扫描我的手机寻找我可以摆脱的东西(我一直生活在设备内存的边缘)，我发现我的简单规划-扑克应用程序占用了几乎 20 MB 的存储空间，只是为了提供上面提到的功能集。

*“20mb？真的吗？对于我可以用几行 CSS 和 JS 构建的东西？”* -我，就在我决定在假期为一个规划扑克 PWA 建立一个原型的时候。

如果你读过我以前的帖子，你可能已经知道我是 PWAs 的粉丝。在我看来，无数的本地应用也可以被实现为 PWA，我希望 PWA 的数量会继续增长！另一方面，每当我做这样的项目时，我也专注于学习新的东西。前端项目非常适合这一点，因为我主要做后端开发，所以在构建了这个基于 React 的 PWA 之后，这里是我的一些主要收获。

## 1。用 CSS 可以吗？

当我开始实现一个`Card`组件时，我考虑了翻转它的可能方法。我的第一个方法是通过 CSS 翻转卡体，并根据卡的状态渲染`Front`或`Back`孩子。那是我在 CSS 中对[视角](https://developer.mozilla.org/en-US/docs/Web/CSS/perspective)有了更多了解的时候。基本想法很简单:

*   将背面组件旋转 180 度，保持正面

```
.card-front, .card-back {
    ...
}

.card-front {
  transform: rotateY(0deg);
}

.card-back {
  transform: rotateY(180deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

*   翻转**父**容器，在前/后之间来回切换
*   根据`flipped`状态，渲染`Front`或`Back`组件

我最终放弃这种方法的原因是时机问题。一旦状态改变，就会呈现不同的组件。这意味着在实际翻转过程中，我们已经可以看到“另一面”。我只是不想打乱与 CSS 转换持续时间一致的超时来正确地在组件之间切换，所以在阅读了本文之后，我选择了另一个不涉及组件切换的解决方案。

我们像以前一样保留旋转的东西，但是不是根据状态渲染不同的组件，而是通过`absolute`定位**覆盖**的正面和背面。现在`Card`的两侧同时被渲染，所以我们还必须关闭[背面可见性](https://developer.mozilla.org/en-US/docs/Web/CSS/backface-visibility)。这样我们就不用处理时机问题，我们的`Card`翻转也没问题！:)结合[类名](https://www.npmjs.com/package/classnames)，我们可以无缝地将翻转机制连接到组件状态:

```
<div
className={classNames(
    "card-body",
    { flipped: this.state.flipped },
    this.props.className
)}
onClick={this.props.onSelect || this.flip}
>
    <Front ... />
    <Back ... />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 2。设备运动

让我的卡根据设备动作翻转比我想象的要容易。在 [codepen.io](https://codepen.io) 上快速搜索*“device motion”*给了我[这支笔](https://codepen.io/Volorf/pen/BoVRyb)。因此，根据手机的倾斜度，我得到正值或负值。完美！

读取[devicemotionevent . acceleration](https://developer.mozilla.org/en-US/docs/Web/API/DeviceMotionEvent/acceleration)或[devicemotionevent . acceleration including gravity](https://developer.mozilla.org/en-US/docs/Web/API/DeviceMotionEvent/accelerationIncludingGravity)我通过检查振幅和符号是否有变化来检测抖动。当达到阈值时，就会执行`onShake`回调。还有一个额外的超时来防止`Seismograph`不停地触发。

看到我的卡在设备动作上翻转，并意识到在 web 应用程序中使用本机设备功能是多么容易，这让我笑得很开心！:)

## 3。托管？GitHub 页面！

前阵子我创建了[另一个 PWA](https://s1hofmann.github.io/xkcdpwa/) 随机浏览 [xkcd 漫画](https://xkcd.com/)。当时，在 GitHub 页面上托管基于 React 的 PWA 需要一些配置才能让一切正常工作。

从那以后事情就变了！:)

*   在您的`package.json`中配置一个`homepage`

```
"homepage": "https://s1hofmann.github.io/planning-poker", 
```

Enter fullscreen mode Exit fullscreen mode

*   安装`gh-pages`包

```
yarn add -D gh-pages 
```

Enter fullscreen mode Exit fullscreen mode

*   添加两个脚本:

```
"predeploy": "npm run build",
"deploy": "gh-pages -d build" 
```

Enter fullscreen mode Exit fullscreen mode

*   运行`yarn deploy`

按照这四个步骤，一切都应该开箱即用，你的 PWA 就上线了。

## 4。有用的东西

我只为我的应用程序创建了一个基础图标。这个工具为我做了繁重的工作，并为我创造了所有其他的尺寸。不错！

# Sooo …

正如你在这里看到的，我的小实验结果相当不错。(嗯…漂亮…至少我是这么认为的)

我已经放弃了我的原生规划扑克应用，也许在不久的将来会有更多！

再见

西蒙