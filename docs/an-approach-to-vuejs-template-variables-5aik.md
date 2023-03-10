# (一种方法)Vue.js 模板变量

> 原文：<https://dev.to/loilo/an-approach-to-vuejs-template-variables-5aik>

## 问题

有时，我需要将方法调用的结果临时存储在 Vue 模板中。这在循环内部尤其常见，在那里我们不容易使用计算属性。

基本上我们想要避免的是:

```
<!-- List.vue -->
<ul>
  <li v-for="id in users" :key="id">
    <img :src="getUserData(id).avatar"><br>
    🏷️ {{ getUserData(id).name }}<br>
    🔗 {{ getUserData(id).homepage }}
  </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

## 常见解决方案

我们可以将这个问题描述为“带有参数的计算属性”，它已经有了一些既定的解决方案:

### 外包组件

非常规范的方式是通过重构完成的:我们可以将`<li>`项外包到它们自己的`<ListItem>`组件中。

该组件将接收`id`作为一个属性，并将相应的元数据存储在一个计算的属性中，然后由 Vue 缓存该属性，直到需要重新计算它。

```
<!-- List.vue -->
<ul>
  <ListItem v-for="id in users" :key="id" :id="id" />
</ul>

<!-- ListItem.vue -->
<li>
  <img :src="metadata.avatar"><br>
  🏷️ {{ metadata.name }}<br>
  🔗 {{ metadata.homepage }}
</li> 
```

Enter fullscreen mode Exit fullscreen mode

然而，这种方法的编写和维护非常繁琐:我们在每个列表项中需要的所有数据都必须作为道具传递给`<ListItem>`。

作为读者，这也很难理解——尤其是当`<ListItem>`组件非常小的时候。那么它可能很容易包含四行模板代码，后跟 25 行 props 定义样板文件。

### 记忆方法结果

我们也可以记住`getUserData()`的结果。

然而，这也可能很难实现，它通常只适用于可序列化的输入数据——在所有方法中，在 Vue 之上添加另一层记忆感觉最不适合 Vue way。

## 我的方法

对于我的项目，我喜欢使用另一种(不太明显，并且 <abbr title="as far as I can tell">AFAICT</abbr> 不太常见)方法:我创建一个助手组件，我称之为`<Pass>`。

它真的非常非常小:

```
const Pass = {
  render() {
    return this.$scopedSlots.default(this.$attrs)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，这是一个占位符组件，它本身并不呈现 DOM 元素，而是将它接收到的所有属性传递给它的子元素。

所以，让我们用`<Pass>`助手:
重写我们的列表

```
<!-- List.vue -->
<ul>
  <Pass v-for="id in users" :key="id" :metadata="getUserData(id)">
    <li slot-scope="{ metadata }">
      <img :src="metadata.avatar"><br>
      🏷️ {{ metadata.name }}<br>
      🔗 {{ metadata.homepage }}
    </li>
  </Pass>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

这只对`getUserData()`求值一次:当`<Pass>`被渲染时。又好又干净，不是吗？

此外，这里有一个代码沙箱，你可以摆弄我描述的例子:

[https://codesandbox.io/embed/1v50nxwx77](https://codesandbox.io/embed/1v50nxwx77)

### 告诫

老实说，这种方法有一些缺点:

*   助手组件利用一个[作用域槽](https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots)来传递数据。这意味着，`<Pass>`只能有一个子组件。
*   这种方法的另一个限制是注入插槽*的标记必须*呈现一个真正的 DOM 节点。我们不能把`slot-scope`设置在像`<template>`这样的东西上。

就是这样。我希望这有助于简化你的 Vue 模板！