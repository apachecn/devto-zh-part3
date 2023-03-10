# 使用 vue.js 的可访问路由

> 原文：<https://dev.to/marcush/accessible-routing-with-vuejs-ea>

路由是单页应用程序不可或缺的一部分，因此对于我的副业[accessible-app.com](http://accessible-app.com)。因为它对单页应用程序的包容性非常重要，所以它是我决定解决的第一个可访问应用程序版本 1 的承诺功能。此外，在我想在这个项目中介绍的所有大型 JavaScript 框架中，我最熟悉的是 Vue。所以我试着以一种容易理解的方式使用官方路由器 [vue-router](https://router.vuejs.org/) 。

正如我之前提到的，React 在即插即用解决方案和在其框架中记录可访问解决方案方面领先于 Vue。其中一个现成的解决方案是 [Reach UI](http://reach.tech/) ，具体来说就是[瑞安佛罗伦萨](https://twitter.com/ryanflorence/)的 [Reach 路由器](https://reach.tech/router)。在 [Reach Router 的产品页面](https://reach.tech/router/accessibility)上，他总结了为什么开发者必须意识到不从整体上刷新页面会带来的可访问性问题:

> 每当页面内容响应用户交互而改变时，焦点应该转移到该内容上；否则，使用辅助设备的用户不得不在页面上四处搜索，寻找发生了什么变化——恶心！如果没有路由器的帮助，管理路由转换需要您付出大量的努力和知识。

当您在 React 应用程序中使用 Reach Router 时，它通过手动将焦点设置到新加载内容的容器来负责管理焦点部分。这很好，但是对于我的 Vue 路由方法，我想让它在焦点被发送到的地方是可配置的。例如，你应该能够将焦点发送到加载内容中的一个标题上(正如[谷歌的罗布·多德森简洁地总结的那样](https://dev.to/robdodson/managing-focus-64l)，或者[simplyaccessible.com 使用 Angular](https://simplyaccessible.com/article/spangular-accessibility/#acc-heading-3) 详细地解释了这一点)。

### 访问焦点目标

我们可以用一个引用来标记在路由转换之后我们将把焦点发送到的节点。含义:将`ref`属性放在上面，然后访问它([点击](https://codingexplained.com/coding/front-end/vue-js/accessing-dom-refs)了解更多关于使用$refs 访问 DOM 的信息)。一个例子:

```
<h2 ref="focusTarget">Focus me</h2> // Get the element in Vue with this.$refs.focusTarget 
```

现在我们已经获得了对焦点目标的引用，我们必须找出路由转换发生的时间，并与该事件挂钩。您可以为此使用观察器。但是您必须确保等待 DOM 真正发生变化。这就是 [Vue.nextTick](https://vuejs.org/v2/api/#Vue-nextTick) 的用途:

```
new Vue({
    router,
    watch: {
        $route: function() {
            this.$nextTick(function () {
            // $nextTick = DOM updated

            });
        }
    }
}).$mount('#app'); 
```

### 等待吧...

另一件事是在运行实际的焦点代码之前添加一个延迟。[这显然是由于 Voice Over 未能将焦点设置在 iOS 7 和更早版本的 DOM 节点上](http://dylanb.github.io/bower_components/a11yfy/examples/focus.html)。虽然这似乎在版本 8 中得到修复——因为我可以找到关于该主题的新信息，所以我将添加一个延迟。

现在是中心焦点部分。首先，我们寻找 focusTarget ref。如果您的路线观察者找不到它，我们的焦点目标将是在路线转换后内容将被加载到的容器。Vue 路由器称之为`<router-view>`。为了使这种回退更容易理解，我们将像这样添加对路由器视图的引用:

```
<!-- Here be <router-links />'s -->
<router-view ref="routerView"></router-view> 
```

但是回到 JavaScript:

```
// Get component's "routeFocusTarget" ref.
// If not existent, use router view container itself
let focusTarget =
    (this.$refs.routerView.$refs.componentFocusTarget !== undefined)
        ? this.$refs.routerView.$refs.componentFocusTarget
        : this.$refs.routerView.$el; 
```

在我们最终能够将焦点设置在焦点目标上之前，我们实际上必须确保我们能够以编程方式将焦点设置到它上面(因为通常只有像按钮、链接或表单输入这样的交互元素是可聚焦的)。

```
focusTarget.setAttribute('tabindex', '-1'); 
```

GDS， [gov.uk](https://gov.uk) 背后的团队发现一个包装容器上的“迷失”tabindex-[在他们的案例中，`<main>`元素](https://github.com/alphagov/govuk_elements/pull/534)，这是一个围绕[的黑客行为，一个浏览器漏洞](https://bugs.chromium.org/p/chromium/issues/detail?id=37721)，可能会导致一些问题。因此，我们在最终设置焦点之后删除 tabindex:

```
// Focus element
focusTarget.focus();

// Remove tabindex from focustarget.
focusTarget.removeAttribute('tabindex'); 
```

### 把所有的东西放在一起

我准备了一个代码笔来演示这一点，我把上面提到的所有部分放在一起。在这个例子中,“route target”组件非常简单——其中两个将 componentFocusTarget 显式地设置为它们的第一个标题，一个设置为它们的通用容器 DOM 节点，还有一个根本没有这样的 ref。但无论如何——焦点是在路线改变后处理的。出于调试和显示的目的，我用一个红色边框使焦点可见。但是，仅仅将注意力转移到路由更改上还不足以实现可访问的路由。一旦你在你的单页应用中改变了 URL，**你必须调整文档的标题**。

### 演示

[https://codepen.io/marcus/embed/WYEypM?height=600&default-tab=result&embed-version=2](https://codepen.io/marcus/embed/WYEypM?height=600&default-tab=result&embed-version=2)

通过这种方式，我们防止了上面 Ryan Florence 描述的情况，即辅助技术的用户与路由链接交互，焦点停留在所述链接上，尽管 DOM 的部分已经改变，并且他们需要主动搜索这些改变。

### vue 路由器中的本地焦点管理

我在官方的 vue-router 报告中提交了一份关于 focus 主题的功能请求。 Vue 核心成员 Eduardo San Martin Morote 回答道:

> 谢谢你提出讨论。关于这一点，我已经关注 reach 路由器有一段时间了，这是我们应该能够提供的东西

不必像上面提到的那样添加策略，而是发现这个已经内置到 vue-router 中的策略将会非常棒！

最后，我很好奇你对这个解决方案的看法。退一步说，这里介绍的温泉路线的焦点管理模式是一个最佳实践，但是围绕它的所有假设都应该进行测试([，如果 Deque Systems、Marcy Sutton 和 accessibility community 能够进行这样的测试就太好了](https://twitter.com/marcysutton/status/1047892352747036672)。在此之前，请不要犹豫，告诉我您对这种路由和焦点管理方法的看法，以及它可以改进的地方。

## 更新:将该技术应用到一个示例应用程序中

我为#accessibleapp 研究了这个路由策略，这是一个兼职项目，我收集了构建包容性 JavaScript web 应用程序的技术(在 React、Vue 和 Angular 中)。为了提供一个切实的例子，我构建了一个名为“Accessibooks”的示例应用，你可以在 https://vuejs.accessible-app.com 找到 Vue 的实现