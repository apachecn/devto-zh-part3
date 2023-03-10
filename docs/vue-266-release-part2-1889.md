# Vue 2.6.6 版本 part2 新增了 v-slot 语法

> 原文：<https://dev.to/f3ltron/vue-266-release-part2-1889>

正如我在我的上一篇[文章](https://dev.to/f3ltron/vue-266-release-part1-23b4)中所说，我们将举一个简单的例子，并尝试重现由 Posva 制造的令人敬畏的 vue-promise 组件[的行为。](https://github.com/posva/vue-promised)

我们想在这里实现什么？

1)创建一个组件承诺
2)它应该在等待时显示加载
3)它应该在出错时显示错误。这里我们需要一个作用域槽来显示错误。消息
4)当数据加载无误时显示数据

💪 🤘不要跑！！！😄

**你可以随时看到最终代码:**

*   [老槽](https://codesandbox.io/s/github/f3ltron/simple-vue-promise-old/tree/master/)
*   [新插槽](https://github.com/f3ltron/simple-vue-promise.git)

# 旧槽和槽-范围

```
// App.vue
<template>
  <div>
    <PromiseComponent :promise="childrenPromise">
      <span slot="pending">Loading... !</span>

      <template slot-scope="{ data }">
        <ul>
          <element v-for="name in data" :key="name"> {{ name }} </element>
        </ul>
      </template>

      <p slot="rejected" slot-scope="{ error }">{{ error.message }}</p>
    </PromiseComponent>
  </div>
</template> 
```

# 新插槽作用域插槽语法

```
// App.vue
<template>
  <div>
    <PromiseComponent :promise="childrenPromise">
      <template #pending>
        <span >Loading... !</span>
      </template>

      <template #default="{data}">
        <ul>
          <Element v-for="name in data" :key="name">
            {{ name }}
          </Element>
        </ul>
      </template> 

      <template #rejected="{error}">
        <p>Error... :( : {{ error.message }}</p>
      </template>
    </PromiseComponent>
  </div>
</template> 
```

它们都有相同的子文件:

```
// /components/Promise.vue
<template>
  <div>
    <slot v-if="isLoading" name="pending" :isLoading="isLoading"></slot>
    <slot v-if="!isLoading" name="default" :data="data"></slot>
    <slot v-if="error" name="rejected" :error="error"></slot>
  </div>
</template> 
```

我已经把最重要的文件放在这里了。如果您想查看更多信息，请访问:

*   [老槽](https://codesandbox.io/s/github/f3ltron/simple-vue-promise-old/tree/master/)
*   [新插槽](https://github.com/f3ltron/simple-vue-promise.git)

是的，有很多密码！但是没关系，相信我😄

对于旧的语法，我向你展示了你可以用不同的方式来写它们。这是一个没有很多嵌套组件的基本示例...对我来说，这似乎很混乱..

有时使用这种语法，限定作用域的槽可能真的很奇怪，因为你不知道 slotProps 来自哪里。我不喜欢的是你必须写两个指令 slot 和 slot-scope。

它以前工作得很好，但还可以更好！😄

在新的语法中，他们最终决定将它们重组为一个指令。v 型槽。

**重要 1:你需要知道 v-slot 只能在`<component />`或`<template/>` html 标签中使用。不能用在`<p>`举例**

我喜欢在一个 v-slot 中重新组合所有内容的想法:默认

(#default 是 v-slot:default 的简写)

```
#:[slotNAme]=[slotPros] 
```

我非常喜欢的最后一点是性能改进！

目前根据 [EvanYou](https://medium.com/the-vue-point/vue-2-6-released-66aa6c8e785e)

> 普通窗口在父窗口的渲染周期中渲染。当插槽的依赖关系改变时，会导致父组件和子组件重新呈现

这意味着什么？

[查看此处](https://codesandbox.io/s/2olpjzkmkj)

正如你打开控制台时看到的。即使您更新了父插槽或子插槽中的数据，它也会更新正常范围。这是性能问题点由埃文你。

但是等等...插槽范围没有更新！？

是的。据埃文说，你

> 另一方面，作用域插槽被编译成内联函数，并在子组件的呈现周期中被调用。这意味着子组件收集作用域槽所依赖的任何数据依赖，从而导致更精确的更新

所以这里会更新他的孩子什么时候更新而不是和他的父母一起更新！

使用新的 v-slot 语法的所有槽都被编译到作用域槽中。所以有了新的语法，你就不用考虑性能问题了。默认情况下会这样做👏 👏 👏

感谢阅读:)。

如果你想关注我或者知道我实际上在做什么！

*   [我的推特](https://twitter.com/giraud_florent)
*   [我的实际工作](https://f3ltron.github.io/vuepress-component-docgen/)
*   [我的 github](https://github.com/f3ltron)
*   [我的企业](https://www.atecna.fr)

**PS** :如果你想了解更多关于发布[的信息，来这里](https://medium.com/the-vue-point/vue-2-6-released-66aa6c8e785e)