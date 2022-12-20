# VUE 的一个观点和一个实例。

> 原文：<https://dev.to/claeusdev/a-point-of-vue-with-an-instance-321o>

作为前端工程师，我们在工作中处理的大多数问题都是将数据渲染到我们的视图中。多年来，实现这一点的最佳方法已经出现了很多次，但后来出现了 VUE.js. Vue 通过提供一个非常易于使用的 API 来将数据渲染到我们的视图中，从而帮助我们这些前端工程师和开发人员。这篇文章是向绝对初学者介绍 Vue 框架的系列文章的一部分。在这篇文章中，我将介绍`vue`实例。

## Vue 是什么？

综上所述，Vue 是一个反应式的、通用的和高性能的前端框架，它提供了一个支持简单维护和可测试性的 API。Vue 的制作者让它变得如此简单，以至于你可以只在你的视图的一小部分放下 Vue，从而使它变得非常进步。不像 Ember(我最喜欢的一个)和 Angular 这样的框架，Vue 不需要在你需要的地方控制你的整个 UI。它深受 MVVM 模式的启发。

## 入门。

每个`vue`应用程序都以一个函数 Vue `instance`开始，这是我们今天的主题。Vue 的一个实例类似于:

```
const vm = new Vue({
    'el': '#app',
    'data': {
        message: "Hello World!"
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 实例

Vue 实例是一个将一个*选项*对象作为参数的函数。这基本上是`vue`应用程序的根。options 对象只是存储数据和执行操作。*选项的*对象由:

*   *【El】*:options 对象的这个属性帮助我们将实例连接到 DOM(文档对象模型)。因此，值`'#app'`意味着我们将实例绑定到 DOM 上一个 id 为 *app* 的*元素。*

*   *data* :这个属性是一个对象，可以用来将数据传递到 DOM 中。当一个实例被创建时，在对象中发现的所有属性被添加到一个叫做*反应系统*的东西中。正如我前面所描述的，Vue 是反应性的，这基本上意味着它同时对变化做出反应。反应系统的工作方式是，实例的*数据*被绑定或链接到数据被引用的任何地方。当数据对象中的某个值发生变化时，DOM 上对它的所有引用的值都会“作出反应”,然后更新以匹配变化。注意:属性只有在用实例创建时才起作用，这意味着当你在实例创建后添加一个新属性时，对这个属性的任何改变都不会引起 dom 的改变。

*   *方法*:除了数据对象及其属性，实例还提供了许多有用的实例属性和方法。用前缀`$`表示，主要是为了区分实例方法和用户定义的方法。让我们看看下面这个来自官方 Vue 文档的例子:

```
 let data = { a: 1 }
    let vm = new Vue({
      el: '#example',
      data: data
    })

    vm.$data === data // => true
    vm.$el === document.getElementById('example') // => true

    // $watch is an instance method
    vm.$watch('a', function (newValue, oldValue) {
      // This callback will be called when `vm.a` changes
    }) 
```

Enter fullscreen mode Exit fullscreen mode

*   *生命周期钩子*:就像任何其他框架一样，Vue 也有自己的过程，一个实例通过这个过程来完成它的实例化。从设置数据、编译模板到在 DOM 上挂载实例。然后，它必须用新的变化来更新 DOM。在实例的生命周期中，也就是从实例化开始到将实例安装到 DOM，Vue 通过*生命周期挂钩*来公开函数，这允许开发人员基于实例生命周期中的特定阶段来执行某些操作。典型的例子是`created`，它在实例创建后运行。其他的包括`mounted`、`updated`和`destroyed`，它们描述了 Vue 实例生命周期中的角色或功能。如果你以前做过反应，这些可能听起来很熟悉`component lifecycle method`。同样值得注意的是，这些方法中的每一个都拥有它们被调用的实例的上下文。

嗯，这是对 VUE 实例的一个非常简短的介绍，它是任何 vue 应用程序的根。在随后的文章中，我将通过更好的例子深入探讨该实例的每个组件。此外，如果这激发了你尝试 Vue 或学习更多相关知识的兴趣，请[查看 Vue 文档](https://vuejs.org/)，如果你喜欢，也可以关注我，获取更多这些帖子。