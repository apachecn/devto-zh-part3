# 茶歇期间的 Vue 使用带有定制组件的 v-model。

> 原文：<https://dev.to/vue-storefront/vue-during-coffee-break-using-v-model-with-custom-components-3bo9>

本系列的目的是发布关于高级 Vue 概念的提示和技巧，它们可以快速应用于每个应用程序，并为您提供解决问题的新武器。

在这篇短文中，我将解释`v-model`是如何工作的，以及它如何应用于每一个 Vue 组件。

## 了解 v-模型

`v-model`是几乎每个 Vue 应用中使用的通用指令。它通常用于在表单元素上启用双向数据绑定，并与`input`、`checkbox`、`select`、`textarea`和`radio`完美配合。

在下面的例子中，应用于`input`元素的`v-model`将`someVal`变量与输入的本地`value`属性绑定在一起。

```
<input v-model="someVal"> 
```

Enter fullscreen mode Exit fullscreen mode

然后，该指令监听本机`input`事件，并在每次发出事件时更新`someVal`。

所以事实证明，我们可以把上面的代码改写成同样效果的知名事件和道具:

```
<input
  v-bind:value="someVal"
  v-on:input="someVal = $event.target.value"
> 
```

Enter fullscreen mode Exit fullscreen mode

这就是`v-model`应用于常规输入的方式。

知道了这一点，我们就可以在每个将发出`input`事件并接受`value`道具的组件上使用`v-model`。

看看这个`MagicCounter` :

```
<template>
  <div>
    <button @click="changeValue(value-1)">-</button>
    <span>{{ value }}</span>
    <button @click="changeValue(value+1)">+</button>
  </div>
</template>

<script>
export default {
  props: ["value"],
  methods: {
    changeValue(newVal) {
      this.$emit("input", newVal);
    }
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

因为我们在每次改变时都用一个新值发出`input`事件，并接受`value`属性，所以我们可以安全地在这个组件上使用`v-model`指令:

```
<MagicCounter v-model="count" /> 
```

Enter fullscreen mode Exit fullscreen mode

## 使用带有自定义组件的 v-model

Event thought `input`和`value`对是`v-model`的默认设置，取决于输入类型，这些绑定可以不同(我强烈建议检查[的源代码](https://github.com/vuejs/vue/blob/6fe07ebf5ab3fea1860c59fe7cdd2ec1b760f9b0/src/platforms/web/compiler/directives/model.js)以获得详细信息)。例如在`checkbox`元素中，使用了`checked`属性和`change`事件，而不是默认的属性和事件。

原来我们通过一个`model`属性定制了被`v-model`指令接受的事件/属性对。例如，这是`checkbox`元素:
的样子

```
model: {
  prop: 'checked',
  event: 'change'
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能希望将我们的`MagicCounter`发出的事件的名称改为更具描述性的名称(例如`modified`)。

让我们看看如何使用`v-model`
让这个自定义事件工作

```
<template>
  <div>
    <button @click="changeValue(value-1)">-</button>
    <span>{{ value }}</span>
    <button @click="changeValue(value+1)">+</button>
  </div>
</template>

<script>
export default {
  props: ["value"],
  model: {
    event: `modified`
  },
  methods: {
    changeValue(newVal) {
      this.$emit("modified", newVal);
    }
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

..瞧！现在你知道如何对每个 Vue 组件使用`v-model`。我希望你能很快找到运用这些知识的方法。

[在这里](https://codesandbox.io/s/0yo61k5zrl?fontsize=14)你可以从文章中找到一个代码工作的例子来玩。

请继续关注该系列的下一部分！