# 增强 Vue 应用的混合和自定义功能

> 原文：<https://dev.to/bnevilleoneill/mixins-and-custom-functions-to-enhance-your-vue-applications-10p6>

[![](img/60e0431d89d192aa5d42d44d883687e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bjLGdTQ9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/400/1%2A1sQo1N226uH5Ha8Q-hiM5A.jpeg)

如果你是一个 Vue 爱好者(像我一样)，并且正在寻找一种方法来扩展你的 Vue 应用，那么你来对地方了。Vue **mixins** 和 **directives** 是一个强大的组合，是在应用程序的不同部分添加更多可重用功能的好方法。

如果你来自面向对象的编程背景，那么你会看到 Vue mixins 是对父类的模仿。您还将看到指令类似于帮助函数。

如果你没有 OOP 背景，那么可以把 mixins 看作是一个可以被多人共享的工具。如果你在考虑一个办公室，那应该是复印机。如果你正在考虑一个购物中心，它会是购物中心的安全。基本上，mixins 是应用程序的多个部分共享的资源。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 先决条件

1.  JavaScript 知识
2.  至少，您已经构建了一个 Vue 应用程序。一个超过 5 个组件是一个加号
3.  如果你共用办公室的复印机，你可以坐在这里的前面

### Mixins

Vue 文档对混音是什么以及它们如何工作有一个非常简单明了的解释。根据文档，mixins 是一种为 Vue 组件分发可重用功能的灵活方式。mixin 对象可以包含任何组件选项。当一个组件使用 mixin 时，mixin 中的所有选项都将“混合”到组件自己的选项中。

更简单地说，这意味着我可以用它的数据、方法、生命周期组件创建一个组件，并让其他组件扩展它。现在，这不同于在其他组件中使用组件，在其他组件中，您可以拥有一个名称类似于模板内部的自定义组件。

让我们来看看。

**我们的 mixin** —这个 mixin 将保存我们应用的基本配置，例如:

*   应用名称
*   更好的方法
*   页脚的版权公司名称

让我们创建一个简单的 mixin:

[https://medium . com/media/62a 3197 df 7 FDE 6 DD 17 D2 d 34 E6 f 9 ed 185/href](https://medium.com/media/62a3197df7fde6dd17d2d34e6f9ed185/href)

好的。这对于混音来说再简单不过了。现在，如果我们在组件中使用它，您将会看到它的神奇之处。

为了使用它，我们可以在我们的模板中做以下事情:

```
new Vue({
  mixins: [myMixin],
  el: '#app'
}); 
```

### 指令

另一方面，指令是像 v-for 这样的方法，您可以创建它来修改模板上的元素。你知道如果不满足某个条件，v-if 会隐藏你的组件吗？我们用一个指令在一个长句下面划线怎么样。我们甚至可以稍微改变一下文字来突出它。

我们可以注册全局指令，以便 Vue 应用程序中的所有组件都可以使用它。我们还有特定于该特定组件的本地指令。太棒了，对吧？

现在让我们创建一个全局指令:

[https://medium . com/media/46188 a 16 b 82 c 72 fc 9 AC 0d 65 f 4 CAFA 45 c/href](https://medium.com/media/46188a16b82c72fc9ac0d65f4cafa45c/href)

现在，如果我们使用这个指令，您应该会看到文本的某些部分发生了变化。

为了使用它，我们可以在模板中做以下事情:

```
<template>
  <div>
    <p v-highlight>Hello There!</p>
    <p v-highlight="red">This is a red guy</p>
  </div>
</template> 
```

### 滤镜

这是我们要看的另一个定制助手。过滤器在很多方面帮助了我们(如果这是你第一次遇到它们，你可能会因为没有早点知道而生气)。我们可以全局或局部定义过滤器，就像指令一样。

过滤器可用于对文本应用通用格式，或对数组或对象进行大量过滤。它们是 JavaScript 函数，所以我们可以将它们定义为接受尽可能多的参数。此外，我们可以将它们链接起来，并使用多个过滤器。酷吧？

让我们定义一个简单的过滤器，将正文的第一个单词大写(这在显示用户提供的姓名时非常有用):

[https://medium . com/media/a 02206 CB 907 c 20 a2 eab 83 df 4d 7366 b 59/href](https://medium.com/media/a02206cb907c20a2eab83df4d7366b59/href)

为了使用它，我们可以在我们的模板中做以下事情:

```
<template> 
  <div>
    <p>{{ firstname | capitalize }}</p>
  </div>
</template> 
```

现在，无论我们在哪里使用这个过滤器，第一个字符总是大写。

### 齐心协力

我们将使用我们所学的一切来构建一个简单的 Vue 应用程序。您可以在 codepen 上试用它，并实时使用它，看看您能从中获得什么。

首先，让我们定义我们的 mixin:

[https://medium . com/media/EC 55 da a6 A8 d 8d 0909 e 072 c8 e 465580 f 6/href](https://medium.com/media/ec55daa6a8d8d0909e072c8e465580f6/href)

然后我们定义我们的指令:

[https://medium . com/media/55 af 6 B2 cf 41 b 5c 31 fedc 58d 6511 c0c 63/href](https://medium.com/media/55af6b2cf41b5c31fedc58d6511c0c63/href)

现在，让我们定义我们的过滤器:

[https://medium . com/media/c 6 CDA 4 E1 Fe 34 bef 74 c 19954 a 74 f 06 e 32/href](https://medium.com/media/c6cda4e1fe34bef74c19954a74f06e32/href)

然后，让我们初始化我们的 Vue 应用程序:

```
// [...]
new Vue({
  mixins: [myMixin],
  el: '#app'
}) 
```

最后，简单的模板来看看这些东西是否真的有用:

```
<div id="app">
  <p v-highlight>{{title | capitalize}}</p>
  <p v-highlight="'red'">This is a red guy</p>
  <p>{{'this is a plain small guy' | capitalize}}</p>
<div> 
```

仅此而已。

### 结论

当构建复杂程度可能会增加的应用程序时，我们在这里提到的一切都会派上用场。您希望定义许多可重用的函数，或者以一种可以跨组件重用的方式对它们进行格式化，这样您就不必一次又一次地定义相同的东西。

最重要的是，你想有一个单一的真相来源，奉献一个地方来做出改变。想到你现在可以用这些功能构建很酷的东西，我就很兴奋。请与我分享它们。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *