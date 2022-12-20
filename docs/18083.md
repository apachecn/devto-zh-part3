# CanJS & FeathersJS 频道

> 原文：<https://dev.to/mattchewone/canjs--feathersjs-channels-4gg5>

我最近写了一篇关于如何使用 FeathersJS 的频道来确保正确的实时数据到达正确的用户手中的文章。我想展示如何使用 [CanJS](https://canjs.com/) 做同样的实时娱乐。

我将参考这篇关于如何设置 FeatherJS 通道的文章:

[FeathersJS，Channels & Vuex](https://blog.feathersjs.com/feathersjs-channels-vuex-7548fb5c5d2c)

### 用 CanJS 进行设置

我克隆了这个[回购](https://github.com/canjs/stealjs-example)来开始。

让我们从建立模型开始，这样我们就可以加载数据并获得实时数据。我们首先需要创建一个 feathersConnection，它是一组自定义的 can-connect 行为。